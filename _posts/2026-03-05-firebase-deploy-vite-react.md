---
title: "Vite + React 빌드 후 Firebase Hosting 배포"
date: 2026-03-05
layout: single
categories: [firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Vite + React 프로젝트를 Firebase Hosting에 배포하는 기본 흐름을 정리했습니다. 아래 단계만 따라 하면 됩니다.

## 1. Firebase CLI 설치(전역)

```bash
npm install -g firebase-tools
```

설치 확인:

```bash
firebase --version
```

## 2. 로그인 및 프로젝트 초기화

```bash
firebase login
firebase init
```

`firebase init`에서 꼭 확인할 것:

- `Hosting` 선택
- 배포 폴더는 Vite 기본값인 `dist`
- SPA라면 `single-page app` 설정에 `Yes`

## 3. Vite 빌드(배포 파일 생성)

```bash
npm run build
```

빌드 결과는 `dist/`에 생성됩니다.

## 4. Firebase 배포

```bash
firebase deploy
```

Hosting만 배포하려면:

```bash
firebase deploy --only hosting
```

---

### 추가 정리(요청 내용 보완)

- `firebase init`은 프로젝트 최초 1회만 해도 됩니다.
- React 프로젝트에서 라우터를 쓰는 경우 `single-page app` 설정을 `Yes`로 두는 게 안전합니다.
- `firebase init` 전에 Git 상태를 정리하거나, 불필요한 import 제거 후 커밋해두면 히스토리가 깔끔합니다.
