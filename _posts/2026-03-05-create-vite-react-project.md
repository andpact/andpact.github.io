---
title: "Vite + React 프로젝트 생성하기"
date: 2026-03-05
layout: single
categories: [os]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Vite로 React 프로젝트를 만드는 가장 빠른 방법을 정리했습니다. 아래 예시는 터미널 기준이며, 패키지 매니저는 `npm`을 사용합니다.

## 1. 준비 사항

- Node.js 설치(권장: LTS)
- `npm` 기본 제공

버전 확인:

```bash
node -v
npm -v
```

## 2. 프로젝트 생성

```bash
npm create vite@latest my-app
```

실행 후 선택 예시:

- Framework: `React`
- Variant: `JavaScript` 또는 `TypeScript`

## 3. 의존성 설치 및 실행

```bash
cd my-app
npm install
npm run dev
```

브라우저에서 로컬 주소(예: `http://localhost:5173`)로 접속하면 됩니다.

## 4. 기본 빌드/미리보기

```bash
npm run build
npm run preview
```

- `build`: 배포용 빌드 생성
- `preview`: 빌드 결과를 로컬에서 확인

## 5. 폴더 구조 간단히 보기

- `src/`: 앱 소스 코드
- `public/`: 정적 파일
- `index.html`: 앱 진입점

---

### 참고/팁

- TypeScript를 선택하면 `src/main.tsx`와 `src/App.tsx`가 기본으로 생성됩니다.
- 처음에는 `src/App`만 바꿔가며 익히는 게 빠릅니다.
