---
title: "Google Play 구독 검증과 RTDN 연동 설정하기"
date: 2026-03-13
layout: single
categories: [firebase, androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Google Play 구독 기능을 서버에서 안전하게 운영하려면
단순히 결제 검증 API만 호출하는 수준으로는 부족합니다.
구독 갱신, 취소, 환불, 만료 같은 상태 변경까지 따라가려면
**Google Play Developer API**와 **RTDN(Real-time Developer Notifications)** 설정이 같이 필요합니다.

이 글에서는 아래 흐름으로 설정을 정리합니다.

1. Google Cloud에서 Google Play 검증용 서비스 계정을 만든다.
2. Play Console 사용자 및 권한에 해당 서비스 계정을 추가한다.
3. Play Console에서 해당 서비스 계정에 구독 조회에 필요한 권한을 부여한다.
4. Google Cloud에서 Google Play Developer API(Android Publisher API)를 활성화한다.
5. Google Cloud Pub/Sub에 RTDN용 주제(`play-billing-rtdn`)를 만든다.
6. Pub/Sub 주제에 `google-play-developer-notifications@system.gserviceaccount.com` 권한을 부여한다.
7. Cloud Functions에 구독 검증 함수와 RTDN 수신 함수를 배포한다.
8. RTDN 수신 함수가 사용하는 Cloud Run 서비스 계정에 호출 권한을 부여한다.
9. Play Console의 실시간 개발자 알림 설정에 Pub/Sub 주제를 연결한다.

## 왜 이 설정이 필요한가

Google Play 구독은 한 번 결제 검증만 하고 끝나는 구조가 아닙니다.
사용자가 자동 갱신을 켜 두었는지, 구독이 취소되었는지, 결제가 실패했는지에 따라
서버의 권한 상태도 계속 바뀌어야 합니다.

그래서 보통 아래 두 축을 함께 사용합니다.

- **Google Play Developer API**: 현재 구독 상태를 서버에서 직접 조회
- **RTDN**: 상태가 바뀌었을 때 Google Play가 Pub/Sub로 알림 전송

즉, API는 "지금 상태를 조회하는 용도"이고,
RTDN은 "상태 변경을 빠르게 감지하는 용도"라고 보면 됩니다.

## 1. Google Cloud에서 서비스 계정 만들기

가장 먼저 Google Cloud 프로젝트에서
Google Play 결제 검증용 서비스 계정을 생성합니다.

이 서비스 계정은 서버에서 Google Play Developer API를 호출할 때 사용됩니다.
Cloud Functions 또는 다른 백엔드 런타임에서 이 계정 권한으로 Play 구독 상태를 조회하게 됩니다.

서비스 계정을 만들 때는 용도를 구분하기 쉽게 이름을 정하는 편이 좋습니다.
예를 들면 아래처럼 구성할 수 있습니다.

- `google-play-verifier`
- `play-billing-backend`

서비스 계정을 만든 뒤에는
이 계정의 이메일 주소를 Play Console 쪽에 연결해야 합니다.

## 2. Play Console 사용자 및 권한에 서비스 계정 추가하기

Google Cloud에서 서비스 계정을 만들었다고 해서
바로 Play Console 데이터에 접근할 수 있는 것은 아닙니다.

Play Console의 **사용자 및 권한** 메뉴에서
방금 만든 서비스 계정을 사용자로 추가해야 합니다.

핵심은 Google Cloud의 IAM 권한과
Play Console 권한이 서로 별개라는 점입니다.
둘 중 하나라도 빠지면 API 호출이 실패할 수 있습니다.

## 3. 구독 조회에 필요한 Play Console 권한 부여하기

서비스 계정을 추가한 뒤에는
구독 조회와 구매 검증에 필요한 권한을 부여해야 합니다.

이 단계에서는 최소한 아래 목적을 충족할 수 있어야 합니다.

- 구독 구매 정보 조회
- 결제 상태 확인
- 토큰 기반 검증

권한이 부족하면 서버 코드가 맞아도
Google Play Developer API 응답에서 권한 오류가 발생합니다.

## 4. Google Play Developer API 활성화하기

다음으로 Google Cloud 콘솔에서
**Google Play Developer API(Android Publisher API)** 를 활성화합니다.

이 API가 꺼져 있으면
서비스 계정과 Play Console 권한이 모두 준비되어 있어도
실제 검증 요청은 동작하지 않습니다.

서버에서 구독 검증 시 주로 이 API를 통해
`purchaseToken` 기준으로 현재 상태를 조회하게 됩니다.

## 5. Pub/Sub에 RTDN용 주제 만들기

실시간 개발자 알림을 받으려면
Google Cloud Pub/Sub에 RTDN 전용 주제가 필요합니다.

예를 들어 주제 이름은 아래처럼 만들 수 있습니다.

```text
play-billing-rtdn
```

이 주제는 Play Console의 실시간 개발자 알림 설정과 연결되며,
Google Play가 구독 상태 변경 이벤트를 이 주제로 발행합니다.

## 6. Google Play 시스템 계정에 Pub/Sub 권한 부여하기

Pub/Sub 주제를 만들었다면
Google Play가 이 주제로 메시지를 보낼 수 있도록 권한을 줘야 합니다.

대상 계정은 아래 주소입니다.

```text
google-play-developer-notifications@system.gserviceaccount.com
```

이 계정에 주제 게시 권한이 없으면
Play Console에서 RTDN을 연결해도 실제 알림이 들어오지 않습니다.

즉, 이 단계는 "Google Play가 내 Pub/Sub 주제에 메시지를 넣을 수 있도록 허용하는 작업"입니다.

## 7. Cloud Functions에 구독 검증 함수와 RTDN 수신 함수 배포하기

이제 백엔드 쪽 함수를 준비합니다.
일반적으로는 최소 두 가지 함수가 있으면 운영이 편합니다.

### 구독 검증 함수

앱에서 전달한 `purchaseToken`을 받아
Google Play Developer API로 실제 구독 상태를 조회합니다.

이 함수에서는 보통 아래 작업을 처리합니다.

- 로그인 사용자 확인
- `purchaseToken` 검증
- 구독 상태 저장
- Firestore 또는 DB에 entitlement 반영

### RTDN 수신 함수

Pub/Sub로 전달된 실시간 알림을 받아
구독 갱신, 취소, 만료 같은 이벤트를 후처리합니다.

이 함수에서는 보통 아래 흐름으로 처리합니다.

1. Pub/Sub 메시지를 수신한다.
2. 메시지에 포함된 구매 토큰 또는 구독 정보를 확인한다.
3. Google Play Developer API로 최신 상태를 다시 조회한다.
4. DB에 최종 상태를 반영한다.

중요한 점은 RTDN 메시지 자체만 믿고 끝내지 말고,
항상 API로 최신 상태를 다시 조회해 최종 반영하는 방식이 안전하다는 것입니다.

## 8. Cloud Run 호출 권한 설정하기

Cloud Functions 2세대를 사용하면
내부적으로 Cloud Run 기반으로 함수가 동작합니다.

따라서 RTDN 수신 함수가 사용하는 서비스에 대해
필요한 서비스 계정에 **Cloud Run 호출자** 권한을 부여해야 할 수 있습니다.

이 설정이 빠지면 Pub/Sub와 함수 배포가 끝났더라도
실제 호출 단계에서 권한 오류가 발생할 수 있습니다.

특히 인증이 필요한 함수 구성이라면
어떤 서비스 계정이 호출 주체인지 명확히 확인하는 것이 중요합니다.

## 9. Play Console에서 실시간 개발자 알림 연결하기

마지막으로 Play Console의 **실시간 개발자 알림** 설정 화면에서
앞서 만든 Pub/Sub 주제를 연결합니다.

이 단계까지 끝나면
Google Play 구독 상태 변경 이벤트가 Pub/Sub로 전달되고,
그 메시지를 기반으로 RTDN 수신 함수가 동작할 수 있습니다.

설정을 마친 뒤에는 테스트 결제를 통해
아래 항목을 순서대로 확인하는 것이 좋습니다.

- 검증 함수가 `purchaseToken`으로 상태 조회를 정상 수행하는지
- Pub/Sub에 RTDN 메시지가 들어오는지
- RTDN 수신 함수가 메시지를 처리하는지
- DB 권한 상태가 실제 구독 상태와 일치하는지

## 전체 흐름 요약

정리하면 Google Play 구독 서버 연동은 아래 순서로 진행합니다.

1. Google Cloud에서 Play 검증용 서비스 계정을 만든다.
2. Play Console 사용자 및 권한에 그 계정을 추가한다.
3. 구독 조회와 구매 검증에 필요한 Play Console 권한을 부여한다.
4. Google Play Developer API를 활성화한다.
5. Pub/Sub 주제 `play-billing-rtdn`를 만든다.
6. `google-play-developer-notifications@system.gserviceaccount.com`에 주제 게시 권한을 준다.
7. 구독 검증 함수와 RTDN 수신 함수를 배포한다.
8. 필요한 서비스 계정에 Cloud Run 호출 권한을 부여한다.
9. Play Console에서 실시간 개발자 알림과 Pub/Sub 주제를 연결한다.

이 구성이 완료되면
앱은 결제 후 `purchaseToken`만 서버에 전달하고,
서버는 API 조회와 RTDN을 조합해 구독 상태를 안정적으로 관리할 수 있습니다.

## 같이 보면 좋은 포스트

- [Firebase Cloud Functions로 Google Play 결제 검증하기](/firebase/androidstudio/firebase-cloud-functions-google-play-billing/)
- [Firebase Cloud Functions 시작하기: 설정, 작성, 테스트, 배포](/firebase/firebase-cloud-functions-get-started/)
