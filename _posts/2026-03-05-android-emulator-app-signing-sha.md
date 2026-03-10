---
title: "안드로이드 스튜디오 에뮬레이터 앱 서명 키 인증서(SHA-1, SHA-256) 이해하기"
date: 2026-03-05
layout: single
categories: [androidstudio, firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

안드로이드 스튜디오에서 앱을 실행하다 보면
`SHA-1`, `SHA-256` 값을 등록하라는 안내를 자주 보게 됩니다.

이때 많이 헷갈리는 부분이 있습니다.

- "내 앱은 하나인데 왜 SHA 값을 여러 개 등록해야 하지?"
- "에뮬레이터에서 돌린 앱과 실제 배포 앱이 왜 다르게 취급되지?"

핵심은 **앱 자체가 다른 것이 아니라, 앱을 서명한 키가 다르다**는 점입니다.
Firebase나 Google API는 패키지명만 보는 것이 아니라
**어떤 인증서로 서명된 앱인지**도 함께 확인합니다.

## 핵심 요약

- 에뮬레이터나 USB 디버깅으로 실행한 앱은 보통 **디버그 키**로 서명됨
- 실제 배포 앱은 **릴리즈 키** 또는 **Play App Signing 키**로 서명됨
- 키가 다르면 `SHA-1`, `SHA-256` 값도 달라짐
- Firebase는 이 값을 기준으로 앱을 식별하므로 **배포용 SHA와 디버그용 SHA를 둘 다 등록**해야 함

## 먼저 용어부터 정리

### 앱 서명 키란?

안드로이드 앱은 설치 가능 상태가 되기 전에 반드시 어떤 키로 서명됩니다.
이 서명은 "누가 만든 앱인지", "변조되지 않았는지"를 확인하는 데 쓰입니다.

### SHA-1, SHA-256이란?

서명에 사용된 인증서에서 뽑아낸 대표 지문값이라고 보면 됩니다.
Firebase Console이나 Google Cloud Console에는
실제 인증서 파일 자체를 넣는 대신 이 지문값을 등록합니다.

## 왜 에뮬레이터 앱과 배포 앱을 다르게 봐야 할까?

같은 소스코드로 만든 앱이라도 실행 환경에 따라 서명 키가 달라질 수 있습니다.

### 1. Android Studio에서 바로 실행한 앱

보통 `debug.keystore`로 서명됩니다.

- 개발용
- 로컬 테스트용
- 에뮬레이터/실기기 디버깅용

### 2. Play Store에 배포된 앱

보통 릴리즈 계열 키로 서명됩니다.

- 직접 만든 릴리즈 키
- 또는 Google Play의 `App Signing Key`

이 둘은 서로 다른 키인 경우가 많고,
키가 다르면 인증서 지문도 달라집니다.

즉, Firebase 입장에서는 아래 두 앱이 같은 코드여도 완전히 같은 앱으로 보이지 않습니다.

- `com.example.app` + 디버그 인증서
- `com.example.app` + 릴리즈 인증서

패키지명은 같아도 **서명 인증서가 다르면 다른 앱 인스턴스처럼 취급**된다고 이해하면 됩니다.

## 왜 Firebase에서 둘 다 등록해야 하나?

Firebase는 앱이 요청을 보낼 때
"이 요청이 등록된 안드로이드 앱에서 온 것이 맞는가?"를 확인할 때가 있습니다.

특히 아래처럼 Google 계열 인증이나 앱 검증이 엮이면 서명 정보가 중요해집니다.

- Google 로그인
- Dynamic Links 관련 연동
- 전화번호 인증과 앱 검증
- 일부 Google API 연동
- SHA 등록이 필요한 Firebase/Google 서비스

이때 Firebase 프로젝트에 **배포 앱의 SHA만 등록**돼 있으면,
에뮬레이터에서 실행한 디버그 앱은 다른 인증서로 서명되어 있기 때문에
정상 앱으로 인식되지 않을 수 있습니다.

반대로 **디버그 SHA만 등록**해두면,
스토어에 배포된 실제 앱에서 인증 관련 문제가 날 수 있습니다.

그래서 보통 아래처럼 둘 다 등록합니다.

- 배포 앱용 `SHA-1`, `SHA-256`
- 디버그 앱용 `SHA-1`, `SHA-256`

## 질문에 나온 문장을 풀어서 다시 말하면

> 에뮬레이터의 앱에서 Firestore를 호출하려면 앱 서명 키 인증서가 있어야 함.

정확히 말하면 Firestore 자체는 보안 규칙과 인증 상태가 더 직접적인 요소이지만,
실무에서는 Firebase/Google 연동 과정에서 SHA 등록이 함께 필요한 경우가 많습니다.
그래서 "에뮬레이터에서 테스트할 앱도 Firebase 프로젝트에 등록된 서명 정보와 맞아야 한다"는 뜻으로 이해하면 됩니다.

> 실제 배포된 앱의 앱 서명 키와 에뮬레이터 앱의 앱 서명 키 인증서가 다름.

이 말은
**스토어 앱은 릴리즈/앱서명 키로 서명되고,
Android Studio에서 돌린 앱은 디버그 키로 서명된다**는 뜻입니다.

> 그래서 실제 배포 앱(SHA1, SHA-256) + 에뮬레이터 앱의 앱 서명 키(SHA1, SHA-256)를 등록해야 함.

즉, Firebase 프로젝트에는 보통 다음 네 값을 모두 넣게 됩니다.

- 배포용 `SHA-1`
- 배포용 `SHA-256`
- 디버그용 `SHA-1`
- 디버그용 `SHA-256`

## 예시로 보면 더 쉽다

예를 들어 패키지명이 `com.example.myapp`인 앱이 있다고 해보겠습니다.

- 에뮬레이터 실행본: `com.example.myapp` + 디버그 인증서
- Play Store 배포본: `com.example.myapp` + 릴리즈 인증서

Firebase는 둘 다 같은 패키지명이지만
서명 지문이 다르기 때문에 별도로 허용 정보를 확인합니다.

그래서 콘솔에 릴리즈 SHA만 넣어두면
에뮬레이터 테스트 중 인증 오류가 날 수 있습니다.

## 디버그 키 SHA-1 / SHA-256 확인 방법

에뮬레이터나 Android Studio 실행본은 보통 아래 경로의 `debug.keystore`를 사용합니다.

- macOS/Linux: `~/.android/debug.keystore`
- Windows: `C:\Users\<USER>\.android\debug.keystore`

명령어:

```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

출력에서 `SHA1`과 `SHA-256` 값을 확인해 등록하면 됩니다.

## 릴리즈(배포) 앱 서명 키 확인

배포 앱의 인증서는 다음 두 경우 중 하나에서 확인합니다.

- **Play App Signing 사용 시**: Play Console의 앱 서명 키 화면
- **직접 서명 시**: 릴리즈용 keystore에서 SHA-1/256 추출

릴리즈 keystore에서 확인 예시:

```bash
keytool -list -v -keystore /path/to/release.keystore -alias <alias>
```

## 체크리스트

- 에뮬레이터/디버그 실행용 **디버그 SHA-1/256** 등록
- 스토어 배포용 **릴리즈 또는 App Signing SHA-1/256** 등록
- 패키지명이 Firebase에 등록된 앱과 정확히 같은지 확인
- 인증 오류가 나면 현재 빌드가 정말 디버그인지 릴리즈인지 먼저 확인
- Play App Signing 사용 중이면 로컬 업로드 키와 앱 서명 키를 혼동하지 않기

---

### 마무리

정리하면,
**같은 앱이라도 어떤 키로 서명했는지에 따라 Firebase가 다르게 인식할 수 있습니다.**

따라서 에뮬레이터에서 테스트하는 앱과 실제 배포 앱을 모두 정상 동작시키려면
각 환경의 `SHA-1`, `SHA-256` 값을 Firebase에 함께 등록해야 합니다.
