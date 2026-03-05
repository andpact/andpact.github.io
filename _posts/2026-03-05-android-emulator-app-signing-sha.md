---
title: "안드로이드 스튜디오 에뮬레이터 앱 서명 키 인증서(SHA-1, SHA-256) 정리"
date: 2026-03-05
layout: single
categories: [androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

안드로이드 스튜디오 에뮬레이터에서 앱을 실행할 때, **디버그 앱 서명 키**가 사용됩니다.  
Play Console에 올라간 **실제 배포 앱(릴리즈/앱 서명 키)**과는 다른 키이므로,  
API 사용이나 인증(예: Firebase, Google API, Play Store 호출 등)이 필요하다면 **둘 다 등록**해야 합니다.

## 핵심 요약

- 에뮬레이터(로컬 실행) 앱은 **디버그 키**로 서명됨
- 실제 배포 앱은 **릴리즈 키(혹은 Play App Signing 키)**로 서명됨
- 따라서 **릴리즈 SHA-1/256 + 디버그 SHA-1/256**을 모두 등록해야 정상 동작

## 왜 둘 다 필요한가?

에뮬레이터에서 실행되는 앱은 `debug.keystore`로 서명됩니다.  
반면 스토어에 배포된 앱은 릴리즈 키(또는 Play App Signing 키)로 서명됩니다.  
인증 서버는 **등록된 인증서(SHA-1/256)**만 허용하므로,  
**에뮬레이터 ↔ 실제 배포** 두 환경을 모두 커버하려면 두 키를 전부 등록해야 합니다.

## 디버그 키 SHA-1 / SHA-256 확인 방법

아래 경로의 `debug.keystore`로 확인합니다.

- macOS/Linux: `~/.android/debug.keystore`
- Windows: `C:\Users\<USER>\.android\debug.keystore`

명령어:

```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

출력에서 `SHA1`과 `SHA-256` 값을 확인해 등록하면 됩니다.

## 릴리즈(배포) 앱 서명 키 확인

배포 앱의 인증서는 보통 다음 중 하나에서 확인합니다.

- **Play App Signing 사용 시**: Play Console의 앱 서명 키 화면
- **직접 서명 시**: 릴리즈용 keystore에서 SHA-1/256 추출

릴리즈 keystore에서 확인 예시:

```bash
keytool -list -v -keystore /path/to/release.keystore -alias <alias>
```

## 체크리스트

- 에뮬레이터용 **디버그 SHA-1/256** 등록
- 스토어 배포용 **릴리즈(또는 Play App Signing) SHA-1/256** 등록
- 인증/연동이 안 되면 **등록된 값과 현재 서명 키가 일치하는지** 재확인

---

### 마무리

에뮬레이터에서만 동작이 안 된다면, 대부분 **디버그 SHA 등록 누락**이 원인입니다.  
반대로 스토어 배포 앱에서만 문제가 생긴다면 **릴리즈 키 등록 누락**을 의심하세요.
