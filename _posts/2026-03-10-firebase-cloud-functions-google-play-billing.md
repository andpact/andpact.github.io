---
title: "Firebase Cloud Functions로 Google Play 최초 구매 검증하기"
date: 2026-03-10
layout: single
categories: [firebase, androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

안드로이드 앱에서 Google Play 결제를 붙일 때,
결제창 자체는 **앱 클라이언트**에서 띄우지만
결제 검증과 권한 부여는 **서버에서 처리**하는 편이 안전합니다.

Firebase를 쓰고 있다면 이 서버 역할을 **Cloud Functions for Firebase**로 만들 수 있습니다.
다만 여기서 먼저 구분할 점이 있습니다.

- **최초 구매 직후 검증**: 앱이 전달한 `purchaseToken`을 서버에서 확인하고 권한을 연다.
- **이후 상태 변경 동기화**: 갱신, 취소, 만료 같은 구독 변화는 RTDN으로 따라간다.

이 글은 그중에서 **최초 구매 직후 서버 검증 흐름**만 정리합니다.
즉, 앱이 결제를 끝낸 직후 `purchaseToken`을 서버에 보내고,
Cloud Functions가 Google Play Developer API로 검증한 뒤
Firestore에 구매 상태와 권한을 반영하는 데 필요한 준비 과정을 설명합니다.

RTDN 설정과 운영 흐름은 아래 글에서 따로 다룹니다.

- [Google Play 구독 RTDN 연동 설정하기](/firebase/androidstudio/google-play-subscription-verification-rtdn-setup/)

## 먼저 알아둘 점

가장 중요한 점은 이것입니다.

- **결제 UI는 Cloud Functions에서 띄울 수 없습니다.**
- 결제 플로우 시작은 반드시 **Android 앱 + Google Play Billing Library**에서 해야 합니다.
- Cloud Functions는 그 뒤의 **검증, 상태 저장, 권한 부여**를 담당합니다.

즉, 이 글에서 말하는 "Cloud Functions로 Play 결제를 처리한다"는 뜻은
"앱이 받은 결제 결과를 서버에서 다시 확인하고 안전하게 반영한다"는 뜻에 가깝습니다.

## 이 글에서 다루는 범위

최초 구매 직후 검증만 기준으로 보면 전체 흐름은 아래처럼 단순화할 수 있습니다.

1. Google Cloud에서 Google Play 검증용 서비스 계정을 준비한다.
2. Play Console 사용자 및 권한에 해당 서비스 계정을 추가한다.
3. Play Console에서 해당 서비스 계정에 구매 조회에 필요한 권한을 부여한다.
4. Google Cloud에서 Google Play Developer API를 활성화한다.
5. 앱이 결제 후 `purchaseToken`을 서버로 전달할 수 있게 입력값을 정리한다.
6. Cloud Functions에 최초 구매 검증 함수를 배포한다.
7. 함수가 정상 구매만 Firestore와 사용자 권한 상태에 반영하도록 운영한다.

여기에는 **Pub/Sub**, **RTDN**, **실시간 개발자 알림 연결** 같은 구독 후속 동기화 단계는 포함하지 않습니다.

## 전체 구조

최초 구매 검증만 놓고 보면 구조는 아래처럼 이해하면 됩니다.

### 앱(클라이언트)

- 상품 목록 조회
- 결제창 실행
- 결제 완료 후 `purchaseToken` 획득
- `purchaseToken`, `productId`, `packageName`, `productType`를 Cloud Functions로 전달

### Cloud Functions(서버)

- Firebase Authentication 사용자 확인
- Google Play Developer API로 구매 상태 검증
- 이미 처리한 토큰인지 중복 검사
- Firestore에 구매 원본 데이터 저장
- 사용자 권한(entitlement) 반영
- 필요하면 acknowledge 처리

### Firestore(상태 저장)

- 어떤 사용자가 어떤 상품을 샀는지 저장
- 같은 토큰 중복 처리 방지
- 앱에서 사용할 권한 상태 저장

## 1. Google Cloud에서 검증용 서비스 계정 준비하기

가장 먼저 Google Cloud 프로젝트에서
Google Play 결제 검증에 사용할 서비스 계정을 준비합니다.

이 계정은 Cloud Functions가
**Google Play Developer API(Android Publisher API)** 를 호출할 때 사용됩니다.

이 단계에서 확인할 포인트는 아래와 같습니다.

- 어떤 Google Cloud 프로젝트를 기준으로 운영할지 정한다.
- Play 검증용 서비스 계정 이름을 명확하게 만든다.
- 나중에 Play Console에 연결할 서비스 계정 이메일 주소를 확인해 둔다.

즉, 이 단계는 "서버가 어떤 계정 권한으로 Google Play 구매 정보를 조회할지 정하는 작업"입니다.

## 2. Play Console 사용자 및 권한에 서비스 계정 추가하기

Google Cloud에서 서비스 계정을 만들었다고 해서
바로 Play Console 데이터에 접근할 수 있는 것은 아닙니다.

Play Console의 **사용자 및 권한** 메뉴에서
방금 준비한 서비스 계정을 사용자로 추가해야 합니다.

여기서 중요한 점은
Google Cloud IAM 권한과 Play Console 권한이 서로 별개라는 점입니다.
둘 중 하나라도 빠지면 검증 요청이 실패할 수 있습니다.

## 3. Play Console에서 구매 조회 권한 부여하기

서비스 계정을 Play Console에 추가했다면
실제 구매 검증에 필요한 권한을 부여해야 합니다.

최초 구매 검증 기준으로는 최소한 아래 목적을 만족해야 합니다.

- 앱 상품 구매 정보 조회
- 구독 구매 정보 조회
- `purchaseToken` 기준 상태 확인

여기서 막히면 Cloud Functions 코드를 작성해도
Google Play Developer API 응답에서 권한 오류가 발생합니다.

## 4. Play Console에서 결제 상품을 만들 수 있는 앱 상태 확인하기

최초 구매 검증을 붙이기 전에
Play Console에서 먼저 **인앱 상품**이나 **정기 결제 상품**을 만들어야 하는 경우가 많습니다.

여기서 자주 놓치는 점이 하나 있습니다.
Play Console에서 결제 상품을 만들려면
현재 Play Console에 올라가 있는 앱 버전에
**Google Play Billing Library가 포함되어 있어야 합니다.**

이 조건은 프로덕션 배포본만 해당하는 것이 아니라,
내부 테스트나 클로즈드 테스트 같은 **테스트 트랙 업로드본**에도 똑같이 중요합니다.

즉, 아직 Billing Library가 포함되지 않은 앱 번들만 올라가 있다면
Play Console에서 상품 설정이나 결제 테스트를 진행하려 할 때
흐름이 막힐 수 있습니다.

그래서 이 단계에서는 아래를 먼저 확인하는 편이 좋습니다.

- Play Console에 업로드된 최신 앱 버전에 Billing Library가 포함되어 있는지
- 그 버전이 프로덕션이든 테스트 트랙이든 실제로 등록되어 있는지
- 결제 기능을 테스트할 대상 빌드가 Play Console에 반영되어 있는지

즉, 서버 검증 로직을 만들기 전에
"결제 가능한 앱 버전이 Play Console에 먼저 올라가 있는가"를 확인해야 합니다.

## 5. Google Play Developer API 활성화하기

다음으로 Google Cloud 콘솔에서
**Google Play Developer API(Android Publisher API)** 를 활성화합니다.

이 API가 꺼져 있으면
서비스 계정과 Play Console 권한이 모두 준비되어 있어도
실제 구매 검증 요청은 동작하지 않습니다.

즉, 최초 구매 검증에서 이 단계는 필수입니다.

## 6. 앱에서 서버로 보내야 하는 값 정리하기

결제가 끝난 뒤 앱에서 함수로 보낼 때는
최소한 아래 값이 필요합니다.

- `packageName`
- `productId`
- `purchaseToken`
- `productType` (`inapp` 또는 `subs`)

추가로 서버에서는
현재 로그인한 Firebase 사용자 UID와 이 값을 같이 묶어 저장하는 편이 좋습니다.
그래야 어떤 사용자의 구매인지 안정적으로 연결할 수 있습니다.

이 단계에서 핵심은
"앱이 결제 성공이라고 알려주는 것"이 아니라
"서버가 검증에 필요한 식별자를 정확히 받는 것"입니다.

## 7. Cloud Functions에 최초 구매 검증 함수 배포하기

이제 앱이 전달한 `purchaseToken`을 받아
최초 구매를 검증하는 함수를 배포합니다.

이 함수는 보통 아래 역할을 담당합니다.

- 로그인 사용자 확인
- 필수 파라미터 검증
- Google Play Developer API 호출
- 이미 처리한 토큰인지 검사
- Firestore에 구매 내역 저장
- 사용자 권한 상태 반영
- 일회성 상품이면 acknowledge 처리

중요한 점은
이 함수가 "결제 시작 함수"가 아니라
"결제 완료 후 받은 토큰을 검증하는 함수"라는 점입니다.

## 8. 정상 구매만 상태에 반영하도록 운영하기

최초 구매 검증에서는
Google Play 응답을 확인한 뒤에만 권한을 열어야 합니다.

운영 시 특히 자주 확인하는 포인트는 아래와 같습니다.

- 로그인한 사용자와 `purchaseToken`을 함께 저장하는지
- 같은 `purchaseToken`이 중복 처리되지 않는지
- 검증 전에 앱 권한을 먼저 열지 않는지
- 일회성 상품의 acknowledge를 누락하지 않는지
- 구매 원본 데이터와 사용자 권한 상태를 분리해 저장하는지

예를 들어 아래처럼 나누면 관리가 편합니다.

### `playPurchases/{purchaseToken}`

```json
{
  "uid": "USER_UID",
  "packageName": "com.example.app",
  "productId": "premium_upgrade",
  "productType": "inapp",
  "purchaseToken": "TOKEN_VALUE",
  "purchaseState": 0,
  "acknowledgementState": 1
}
```

### `users/{uid}`

```json
{
  "entitlements": {
    "premium_upgrade": true
  }
}
```

이렇게 나누면
구매 원본 데이터와 실제 앱 권한 상태를 분리해서 관리할 수 있습니다.

## 최초 구매 검증 단계 요약

정리하면 최초 구매 직후 필요한 과정은 아래 순서입니다.

1. Google Cloud에서 Google Play 검증용 서비스 계정을 준비한다.
2. Play Console 사용자 및 권한에 해당 서비스 계정을 추가한다.
3. Play Console에서 해당 서비스 계정에 구매 조회 권한을 부여한다.
4. Play Console에 올라간 앱 버전에 Billing Library가 포함되어 있는지 확인한다.
5. Google Cloud에서 Google Play Developer API를 활성화한다.
6. 앱이 `purchaseToken`, `productId`, `packageName`, `productType`를 서버로 보내도록 준비한다.
7. Cloud Functions에 최초 구매 검증 함수를 배포한다.
8. 검증 성공 후에만 Firestore와 사용자 권한 상태를 반영한다.

여기까지가 **최초 구매 후 서버 검증**에 필요한 범위입니다.
구독 갱신, 취소, 만료 같은 후속 상태 변경은 RTDN 흐름에서 따로 처리해야 합니다.

## 같이 보면 좋은 포스트

- [Google Play 구독 RTDN 연동 설정하기](/firebase/androidstudio/google-play-subscription-verification-rtdn-setup/)
- [Firebase Cloud Functions 시작하기: 설정, 작성, 테스트, 배포](/firebase/firebase-cloud-functions-get-started/)
