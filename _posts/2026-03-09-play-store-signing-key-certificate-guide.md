---
title: "Play 스토어 인증서 정리: 앱 서명 키 인증서 vs 업로드 키 인증서"
date: 2026-03-09
layout: single
categories: [androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Play Console을 처음 다룰 때 가장 헷갈리는 부분이
**앱 서명 키 인증서(App Signing Key Certificate)**와
**업로드 키 인증서(Upload Key Certificate)**의 차이입니다.

이 글에서는 두 인증서의 역할을 명확히 나누고,
내 로컬 키스토어에서 업로드 키(alias)를 확인하는 방법까지 정리합니다.

## 핵심 요약

- **앱 서명 키 인증서**: Google Play가 보관하는 실제 배포 서명 키
- **업로드 키 인증서**: 개발자가 로컬에서 AAB/APK 업로드 시 사용하는 키
- Play Console은 업로드된 파일이 **올바른 업로드 키로 서명됐는지** 검사한 뒤,
  내부적으로 앱 서명 키로 다시 서명해 사용자에게 배포

## 1) 앱 서명 키 인증서 (App Signing Key Certificate)

앱 서명 키 인증서는 Google Play가 직접 관리하는 키입니다.

- 최종 사용자에게 배포되는 APK/AAB는 이 키로 서명됨
- 내 PC의 로컬 키스토어와 직접 연결되지 않음
- 즉, 개발자 로컬의 `.jks` 파일에 있는 키와는 별개로 관리됨

## 2) 업로드 키 인증서 (Upload Key Certificate)

업로드 키 인증서는 개발자가 빌드 산출물을 업로드할 때 쓰는 키입니다.

- 보통 개발자 로컬 키스토어(`.jks`) 안의 alias가 업로드 키에 해당
- Play Console은 업로드된 AAB가 등록된 업로드 키로 서명됐는지 검증
- 검증 통과 후 Google Play가 앱 서명 키로 재서명하여 배포

## 로컬 키스토어에서 업로드 키(alias) 확인하기

아래는 예시 폴더 구조입니다.

- 키스토어 위치(예시): `~/keystore/`
- 키스토어 파일명(예시): `upload-keystore.jks`
- 확인 대상 alias(예시): `upload`

명령어:

```bash
cd ~/keystore/
keytool -list -v -keystore upload-keystore.jks
```

명령 실행 후 키스토어 비밀번호를 입력하면,
출력에서 `Alias name: upload`를 확인할 수 있습니다.

필요하면 alias를 명시해서 바로 조회할 수도 있습니다.

```bash
keytool -list -v -keystore upload-keystore.jks -alias upload
```

## 실무 체크포인트

- Play Console에서 확인하는 **App Signing 인증서 값**과
  로컬에서 확인한 **Upload 인증서 값**은 다를 수 있음 (정상)
- 업로드 실패가 난다면 먼저 업로드 키(alias/keystore) 일치 여부 확인
- 키 분실 시 Play Console의 업로드 키 재설정 절차를 사용

---

### 마무리

정리하면,
사용자 배포용 서명은 **Google Play의 앱 서명 키**,
개발자 업로드 검증용 서명은 **로컬 업로드 키**입니다.

두 키의 역할을 분리해서 이해하면 인증서 관련 오류를 훨씬 빠르게 해결할 수 있습니다.
