---
title: "Firebase Cloud Functions로 Google Play 결제 검증하기"
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
즉, 구조는 보통 아래처럼 잡습니다.

1. 앱에서 Google Play Billing으로 결제를 시작한다.
2. 결제가 끝나면 `purchaseToken`을 받는다.
3. 앱이 이 토큰을 Cloud Functions로 전달한다.
4. Cloud Functions가 **Google Play Developer API**로 결제를 검증한다.
5. 검증이 끝나면 Firestore에 구매 상태를 저장하고, 권한을 부여한다.
6. 필요하면 acknowledge 또는 consume까지 서버에서 처리한다.

이 글에서는 이 흐름을 기준으로
**Firebase Cloud Functions를 이용해 Play Console 결제 시스템을 백엔드에서 처리하는 방법**을 정리합니다.

## 먼저 알아둘 점

가장 중요한 점은 이것입니다.

- **결제 UI는 Cloud Functions에서 띄울 수 없습니다.**
- 결제 플로우 시작은 반드시 **Android 앱 + Google Play Billing Library**에서 해야 합니다.
- Cloud Functions는 그 뒤의 **검증, 상태 저장, 후처리**를 담당합니다.

즉, "Firebase Cloud Functions로 Play 결제를 한다"는 말은 정확히는
"Play 결제 결과를 Cloud Functions에서 안전하게 검증하고 관리한다"는 뜻에 가깝습니다.

## 전체 구조

실무에서는 아래 구조가 가장 이해하기 쉽습니다.

### 앱(클라이언트)

- 상품 목록 조회
- 결제창 실행
- 결제 완료 후 `purchaseToken` 획득
- `purchaseToken`, `productId`, `packageName`, `productType` 등을 Cloud Functions로 전달

### Cloud Functions(서버)

- Firebase Authentication 사용자 확인
- Google Play Developer API로 구매 검증
- 이미 처리한 토큰인지 중복 검사
- Firestore에 구매 내역 저장
- 사용자 권한(entitlement) 부여
- acknowledge 또는 consume 처리

### Firestore(상태 저장)

- 어떤 사용자가 어떤 상품을 샀는지 저장
- 중복 지급 방지
- 구독 상태 동기화

## 준비물

시작 전에 아래 준비가 필요합니다.

1. **Google Play Console**에 앱이 등록되어 있어야 합니다.
2. 앱에 **Google Play Billing Library**가 연동되어 있어야 합니다.
3. Firebase 프로젝트에 **Cloud Functions**와 **Firestore**를 사용할 수 있어야 합니다.
4. Cloud Functions에서 사용할 서비스 계정에 **Google Play Developer API 권한**이 있어야 합니다.

여기서 핵심은 4번입니다.
Cloud Functions에서 Play 결제를 검증하려면,
함수 런타임이 사용하는 서비스 계정을 Play Console의 API 접근 권한에 연결해야 합니다.

보통 흐름은 아래와 같습니다.

1. Google Cloud 프로젝트에서 서비스 계정을 확인한다.
2. Play Console의 **API access**에서 해당 서비스 계정을 연결한다.
3. 상품 조회/구매 검증에 필요한 권한을 부여한다.

이 설정이 빠지면 함수 코드가 맞아도 Play Developer API 호출이 실패합니다.

## 앱에서 서버로 보내야 하는 값

결제가 끝난 뒤 앱에서 함수로 보낼 때는 보통 아래 값이 필요합니다.

- `packageName`
- `productId`
- `purchaseToken`
- `productType` (`inapp` 또는 `subs`)

추가로 서버에서는 **현재 로그인한 Firebase 사용자 UID**를 같이 묶어 저장하는 것이 좋습니다.
그래야 나중에 어떤 사용자의 구매인지 안정적으로 연결할 수 있습니다.

## Cloud Functions 예시

아래 예시는 **2세대 Firebase Callable Function + TypeScript** 기준입니다.
핵심은 `purchaseToken`을 받아서 Google Play Developer API로 검증한 뒤,
정상 구매일 때만 Firestore에 반영하는 흐름입니다.

```ts
import { onCall, HttpsError } from "firebase-functions/v2/https";
import * as admin from "firebase-admin";
import { google } from "googleapis";

admin.initializeApp();

const auth = new google.auth.GoogleAuth({
  scopes: ["https://www.googleapis.com/auth/androidpublisher"],
});

export const verifyGooglePlayPurchase = onCall(async (request) => {
  if (!request.auth) {
    throw new HttpsError("unauthenticated", "로그인이 필요합니다.");
  }

  const uid = request.auth.uid;
  const { packageName, productId, purchaseToken, productType } = request.data;

  if (!packageName || !productId || !purchaseToken || !productType) {
    throw new HttpsError("invalid-argument", "필수 파라미터가 없습니다.");
  }

  const publisher = google.androidpublisher({
    version: "v3",
    auth,
  });

  const db = admin.firestore();
  const purchaseRef = db.collection("playPurchases").doc(purchaseToken);

  const existing = await purchaseRef.get();
  if (existing.exists) {
    return {
      ok: true,
      duplicated: true,
      message: "이미 처리한 purchaseToken입니다.",
    };
  }

  if (productType === "inapp") {
    const result = await publisher.purchases.products.get({
      packageName,
      productId,
      token: purchaseToken,
    });

    const purchase = result.data;

    if (purchase.purchaseState !== 0) {
      throw new HttpsError("failed-precondition", "구매가 완료 상태가 아닙니다.");
    }

    await purchaseRef.set({
      uid,
      packageName,
      productId,
      productType,
      purchaseToken,
      purchaseState: purchase.purchaseState,
      acknowledgementState: purchase.acknowledgementState ?? null,
      purchaseTimeMillis: purchase.purchaseTimeMillis ?? null,
      createdAt: admin.firestore.FieldValue.serverTimestamp(),
    });

    await db.collection("users").doc(uid).set(
      {
        entitlements: {
          [productId]: true,
        },
      },
      { merge: true }
    );

    if (purchase.acknowledgementState === 0) {
      await publisher.purchases.products.acknowledge({
        packageName,
        productId,
        token: purchaseToken,
        requestBody: {},
      });
    }

    return { ok: true, productType };
  }

  if (productType === "subs") {
    const result = await publisher.purchases.subscriptionsv2.get({
      packageName,
      token: purchaseToken,
    });

    const subscription = result.data;

    await purchaseRef.set({
      uid,
      packageName,
      productId,
      productType,
      purchaseToken,
      subscriptionState: subscription.subscriptionState ?? null,
      latestOrderId: subscription.latestOrderId ?? null,
      createdAt: admin.firestore.FieldValue.serverTimestamp(),
    });

    await db.collection("users").doc(uid).set(
      {
        entitlements: {
          [productId]: true,
        },
      },
      { merge: true }
    );

    return { ok: true, productType };
  }

  throw new HttpsError("invalid-argument", "지원하지 않는 productType입니다.");
});
```

## 코드에서 봐야 할 핵심

위 예제에서 중요한 포인트는 아래 5가지입니다.

### 1. 로그인한 사용자와 구매를 묶는다

함수에서 `request.auth.uid`를 사용해
현재 Firebase 사용자와 `purchaseToken`을 연결합니다.
그래야 토큰만 탈취해서 다른 계정에 권한을 붙이는 문제를 줄일 수 있습니다.

### 2. 클라이언트만 믿지 않고 서버에서 다시 검증한다

앱이 "결제 성공"이라고 보내더라도
실제 신뢰 기준은 Google Play Developer API 응답이어야 합니다.
권한 부여는 반드시 서버 검증 이후에 해야 합니다.

### 3. `purchaseToken` 중복 처리를 막는다

같은 토큰이 여러 번 들어오면 권한이 중복 지급될 수 있으므로,
Firestore에서 `purchaseToken`을 문서 ID로 써서 한 번만 처리하도록 막는 방식이 단순하고 안전합니다.

### 4. acknowledge 처리를 빼먹지 않는다

일회성 상품은 검증 후 acknowledge를 해야 합니다.
이 단계가 빠지면 환불 또는 결제 상태 문제로 이어질 수 있으므로,
서버에서 처리하는 편이 안정적입니다.

### 5. 구독은 "처음 검증"만으로 끝나지 않는다

구독은 최초 구매뿐 아니라
자동 갱신, 취소, 만료, 환불 같은 상태 변화가 계속 생깁니다.
그래서 구독 상품은 최초 검증 후에도 상태 동기화 전략이 필요합니다.

## Firestore 저장 예시

예를 들어 문서 구조를 아래처럼 두면 관리가 편합니다.

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

## 구독은 Real-time Developer Notifications까지 같이 보는 편이 좋다

구독 상품을 운영한다면 최초 결제 검증만으로는 부족합니다.
Google Play의 **Real-time Developer Notifications(RTDN)**를 Pub/Sub로 받고,
이를 다시 Cloud Functions에서 처리하면 갱신/취소/보류 같은 상태를 더 정확하게 반영할 수 있습니다.

정리하면 구독은 아래 2단계로 보는 편이 좋습니다.

1. 최초 구매 직후 `purchaseToken`을 Cloud Functions에서 검증
2. 이후 상태 변화는 RTDN + 재조회로 동기화

즉, **일회성 상품은 검증 + acknowledge 중심**이고,
**구독은 검증 + 상태 동기화 중심**이라고 이해하면 됩니다.

## 자주 놓치는 부분

- 테스트 계정을 Play Console 라이선스 테스트에 등록하지 않음
- Cloud Functions 서비스 계정에 Play Developer API 권한을 주지 않음
- 검증 전에 앱 권한을 먼저 열어 버림
- 같은 `purchaseToken`을 여러 번 처리함
- 구독 상태를 최초 구매 결과만 보고 끝냄
- 일회성 상품의 acknowledge/consume 시점을 정리하지 않음

## 마무리

Firebase Cloud Functions를 사용하면
Google Play 결제의 **서버 검증 레이어**를 비교적 간단하게 만들 수 있습니다.

실무 기준으로 보면 역할 분리는 아래처럼 정리됩니다.

- **Android 앱**: 결제 시작, `purchaseToken` 획득
- **Cloud Functions**: 검증, 중복 방지, 권한 부여, acknowledge
- **Firestore**: 구매 내역과 권한 상태 저장

핵심은 "결제 성공 여부를 클라이언트만 믿지 말고,
Cloud Functions에서 Google Play Developer API로 다시 확인한 뒤 권한을 부여한다"는 점입니다.

구독까지 운영할 계획이라면
최초 검증 함수만 만드는 데서 끝내지 말고,
RTDN 기반 상태 동기화까지 같이 설계하는 편이 좋습니다.

공식 문서:

- [Google Play Billing 개요](https://developer.android.com/google/play/billing)
- [Google Play Billing 통합 가이드](https://developer.android.com/google/play/billing/integrate)
- [Google Play Billing 보안 및 구매 검증](https://developer.android.com/google/play/billing/security#verify)
- [Google Play Developer API 시작하기](https://developers.google.com/android-publisher/getting_started)
- [purchases.products.get](https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.products/get)
- [purchases.products.acknowledge](https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.products/acknowledge)
- [purchases.subscriptionsv2.get](https://developers.google.com/android-publisher/api-ref/rest/v3/purchases.subscriptionsv2/get)
- [Firebase Callable Functions 문서](https://firebase.google.com/docs/functions/callable)
