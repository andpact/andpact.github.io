---
title: "안드로이드 스튜디오 프로젝트 .gitignore 정리"
date: 2026-03-09
layout: single
categories: [androidstudio]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

안드로이드 스튜디오 프로젝트를 Git으로 관리할 때는
**빌드 산출물, 로컬 설정, 민감 정보**를 반드시 제외해야 합니다.

이 글에서는 실제로 자주 사용하는 `.gitignore` 항목을
"반드시 제외"와 "상황에 따라 선택"으로 나눠 정리합니다.

## 핵심 원칙

- 재생성 가능한 파일(빌드 결과, 캐시)은 커밋하지 않기
- 개인 PC 환경에 종속된 파일(로컬 설정)은 커밋하지 않기
- 비밀값이 들어간 파일(키스토어, `local.properties` 등)은 커밋하지 않기

## 반드시 `.gitignore`에 넣을 항목

```gitignore
# Gradle / build output
.gradle/
**/build/

# Local SDK path (개인 환경 경로)
local.properties

# IDE files (Android Studio / IntelliJ)
.idea/
*.iml

# OS files
.DS_Store
Thumbs.db

# Signing files (민감 정보)
*.jks
*.keystore

# Logs
*.log
```

## 상황에 따라 선택할 항목

아래 항목은 팀 정책에 따라 포함/제외를 결정하면 됩니다.

```gitignore
# Kotlin/Java crash logs
hs_err_pid*

# Captures and generated artifacts
captures/

# If you use NDK/CMake
.cxx/
externalNativeBuild/

# Firebase / Google service files
# 보통은 커밋하지만, 회사 정책상 분리 관리 시 ignore 가능
# google-services.json
```

## 권장 운영 방식

- `google-services.json`은 개인 앱/테스트 앱이면 보통 커밋
- 회사 프로젝트에서 환경 분리(개발/스테이징/운영)가 명확하면 비공개 관리 검토
- 서명 키(`.jks`, `.keystore`)는 절대 저장소에 올리지 않기
- 키 관련 값은 CI/CD Secret 또는 안전한 비밀 저장소로 관리

## 기존에 잘못 올라간 파일이 있다면

`.gitignore`를 추가해도 이미 커밋된 파일은 계속 추적됩니다.
아래처럼 Git 인덱스에서만 제거해야 반영됩니다.

```bash
git rm --cached local.properties
git rm --cached app/release-key.jks
git commit -m "chore: remove sensitive/local files from tracking"
```

## 마무리

안드로이드 스튜디오 프로젝트의 `.gitignore`는
"빌드 산출물 제외"보다 **"민감 정보 보호"**가 더 중요합니다.

처음 저장소를 만들 때 `.gitignore`를 먼저 잡아두면,
나중에 키 유출이나 불필요한 충돌을 크게 줄일 수 있습니다.
