---
title: "Google Play 구독 RTDN 연동 설정하기"
date: 2026-03-13
layout: single
categories: [firebase, androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Google Play 구독 기능을 서버에서 안정적으로 운영하려면
최초 구매 검증만으로는 부족합니다.
구독은 갱신, 취소, 결제 실패, 보류, 만료처럼
상태 변화가 계속 생기기 때문입니다.

그래서 구독 운영에서는 보통 아래 두 축을 함께 씁니다.

- **최초 구매 검증**: 앱이 전달한 `purchaseToken`을 서버에서 확인한다.
- **RTDN(Real-time Developer Notifications)**: 구독 상태 변경을 Pub/Sub로 받아 후속 처리한다.

이 글은 그중에서 **RTDN 연동 설정에 필요한 과정만** 정리합니다.
즉, 최초 구매 검증용 서비스 계정 연결이나 앱에서 `purchaseToken`을 받는 흐름은 이미 준비되어 있다고 가정하고,
이후 구독 상태 변경을 안정적으로 따라가기 위한 설정만 다룹니다.

최초 구매 검증 흐름은 아래 글에서 따로 볼 수 있습니다.

- [Firebase Cloud Functions로 Google Play 최초 구매 검증하기](/firebase/androidstudio/firebase-cloud-functions-google-play-billing/)

## 이 글에서 다루는 범위

RTDN만 기준으로 보면 필요한 흐름은 아래와 같습니다.

1. Google Cloud Pub/Sub에 RTDN용 주제를 만든다.
2. Pub/Sub 주제에 `google-play-developer-notifications@system.gserviceaccount.com` 권한을 부여한다.
3. Cloud Functions에 RTDN 수신 함수를 배포한다.
4. RTDN 수신 함수가 구독 상태를 다시 조회할 수 있도록 검증 함수 또는 검증 로직 연결 상태를 확인한다.
5. RTDN 수신 함수가 사용하는 Cloud Run 서비스 계정에 호출 권한을 부여한다.
6. Play Console의 실시간 개발자 알림 설정에 Pub/Sub 주제를 연결한다.
7. 테스트 결제로 Pub/Sub, 함수, DB 반영 흐름을 검증한다.

즉, 이 글은 **구독 상태 변경을 받는 인프라와 운영 흐름**에 집중합니다.

## 왜 RTDN이 필요한가

Google Play 구독은 한 번 결제 검증만 하고 끝나는 구조가 아닙니다.
사용자가 자동 갱신을 켜 두었는지, 구독이 취소되었는지,
결제가 실패했는지에 따라 서버의 권한 상태도 계속 바뀌어야 합니다.

그래서 보통 아래처럼 역할을 나눕니다.

- **Google Play Developer API**: 현재 구독 상태를 서버에서 직접 조회
- **RTDN**: 상태가 바뀌었을 때 Google Play가 Pub/Sub로 알림 전송

즉, API는 "지금 상태를 재조회하는 용도"이고,
RTDN은 "상태 변경을 빠르게 감지하는 용도"입니다.

## RTDN 시작 전에 이미 준비되어 있어야 하는 것

이 글에서는 아래 항목이 이미 끝난 상태를 전제로 합니다.

- Google Play 검증용 서비스 계정 준비
- Play Console 사용자 및 권한 연결
- Google Play Developer API 활성화
- 최초 구매 검증 함수 또는 구독 상태 재조회 로직 준비

이 전제가 없으면 RTDN 메시지를 받아도
최종 상태를 안전하게 반영할 수 없습니다.

## 1. Pub/Sub에 RTDN용 주제 만들기

실시간 개발자 알림을 받으려면
Google Cloud Pub/Sub에 RTDN 전용 주제가 필요합니다.

예를 들어 주제 이름은 아래처럼 만들 수 있습니다.

```text
play-billing-rtdn
```

이 주제는 Play Console의 실시간 개발자 알림 설정과 연결되며,
Google Play가 구독 상태 변경 이벤트를 이 주제로 발행합니다.

즉, 이 단계는 "Google Play가 구독 이벤트를 보낼 목적지"를 만드는 작업입니다.

## 2. Google Play 시스템 계정에 Pub/Sub 권한 부여하기

Pub/Sub 주제를 만들었다면
Google Play가 이 주제로 메시지를 보낼 수 있도록 권한을 줘야 합니다.

대상 계정은 아래 주소입니다.

```text
google-play-developer-notifications@system.gserviceaccount.com
```

이 계정에 주제 게시 권한이 없으면
Play Console에서 RTDN을 연결해도 실제 알림이 들어오지 않습니다.

즉, 이 단계는 "Google Play가 내 Pub/Sub 주제에 메시지를 넣을 수 있도록 허용하는 작업"입니다.

## 3. Cloud Functions에 RTDN 수신 함수 배포하기

다음으로 Pub/Sub 메시지를 받을 함수를 배포합니다.

이 함수는 보통 아래 역할을 담당합니다.

- Pub/Sub 메시지 수신
- 메시지에 포함된 구매 토큰 또는 구독 식별자 확인
- Google Play Developer API로 최신 상태 재조회
- Firestore 또는 DB에 최종 상태 반영

여기서 중요한 점은
RTDN 메시지 자체만 믿고 권한 상태를 바로 바꾸지 않는 것입니다.
RTDN은 상태 변경을 감지하는 신호로 쓰고,
최종 반영 전에는 항상 API로 최신 상태를 다시 확인하는 편이 안전합니다.

## 4. 상태 재조회용 검증 로직 연결 상태 확인하기

RTDN 수신 함수만 따로 있어도 충분하지 않습니다.
실제 운영에서는 RTDN을 받은 뒤
구독 상태를 다시 조회할 수 있는 검증 로직이 연결되어 있어야 합니다.

이 단계에서 확인할 포인트는 아래와 같습니다.

- RTDN 수신 함수가 `purchaseToken` 또는 구독 식별자를 꺼낼 수 있는지
- 최신 구독 상태를 Google Play Developer API로 다시 조회할 수 있는지
- 조회 결과를 Firestore 또는 DB 권한 상태에 반영할 수 있는지

즉, RTDN은 단독으로 끝나는 기능이 아니라
"알림 수신 + 상태 재조회 + 최종 반영" 흐름으로 봐야 합니다.

## 5. Cloud Run 호출 권한 설정하기

Cloud Functions 2세대를 사용하면
내부적으로 Cloud Run 기반으로 함수가 동작합니다.

따라서 RTDN 수신 함수가 사용하는 서비스에 대해
필요한 서비스 계정에 **Cloud Run 호출자** 권한을 부여해야 할 수 있습니다.

이 설정이 빠지면 Pub/Sub와 함수 배포가 끝났더라도
실제 호출 단계에서 권한 오류가 발생할 수 있습니다.

특히 인증이 필요한 함수 구성이라면
어떤 서비스 계정이 호출 주체인지 명확히 확인하는 것이 중요합니다.

## 6. Play Console에서 실시간 개발자 알림 연결하기

마지막으로 Play Console의 **실시간 개발자 알림** 설정 화면에서
앞서 만든 Pub/Sub 주제를 연결합니다.

이 단계까지 끝나면
Google Play 구독 상태 변경 이벤트가 Pub/Sub로 전달되고,
그 메시지를 기반으로 RTDN 수신 함수가 동작할 수 있습니다.

즉, 이 단계는
"Play Console과 내 Google Cloud 메시지 경로를 실제로 연결하는 작업"입니다.

## 7. 테스트 결제로 전체 흐름 확인하기

설정을 마친 뒤에는 테스트 결제를 통해
아래 항목을 순서대로 확인하는 것이 좋습니다.

- Pub/Sub에 RTDN 메시지가 실제로 들어오는지
- RTDN 수신 함수가 메시지를 정상 처리하는지
- 함수가 최신 구독 상태를 다시 조회하는지
- DB 권한 상태가 실제 구독 상태와 일치하는지

운영에서는 이 검증 단계를 빼먹으면
설정은 끝난 것처럼 보여도 실제 상태 동기화가 누락될 수 있습니다.

## RTDN 단계 요약

정리하면 구독 상태 변경 동기화에 필요한 과정은 아래 순서입니다.

1. Google Cloud Pub/Sub에 RTDN용 주제를 만든다.
2. `google-play-developer-notifications@system.gserviceaccount.com`에 주제 게시 권한을 부여한다.
3. Cloud Functions에 RTDN 수신 함수를 배포한다.
4. RTDN 수신 후 최신 상태를 재조회할 검증 로직 연결 상태를 확인한다.
5. RTDN 수신 함수가 사용하는 Cloud Run 서비스 계정에 호출 권한을 부여한다.
6. Play Console의 실시간 개발자 알림 설정에 Pub/Sub 주제를 연결한다.
7. 테스트 결제로 Pub/Sub, 함수, DB 반영 흐름을 검증한다.

이 범위가 **RTDN 구현과 운영에 필요한 설정 흐름**입니다.
최초 구매 직후 `purchaseToken` 검증과 권한 부여는 별도 흐름으로 관리하는 편이 구조가 더 명확합니다.

## 같이 보면 좋은 포스트

- [Firebase Cloud Functions로 Google Play 최초 구매 검증하기](/firebase/androidstudio/firebase-cloud-functions-google-play-billing/)
- [Firebase Cloud Functions 시작하기: 설정, 작성, 테스트, 배포](/firebase/firebase-cloud-functions-get-started/)
