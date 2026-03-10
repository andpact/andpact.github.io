---
title: "Firebase Cloud Functions 시작하기: 설정, 작성, 테스트, 배포"
date: 2026-03-10
layout: single
categories: [firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Firebase를 사용하다 보면 어느 순간 "클라이언트 코드만으로는 처리하기 애매한 작업"이 생깁니다.
회원 가입 직후 사용자 정보를 정리하거나, Firestore 문서가 생성될 때 후처리를 하거나, 외부 API를 호출해 결과를 저장해야 하는 경우가 대표적입니다.

이럴 때 사용하는 것이 **Cloud Functions for Firebase**입니다.
서버를 직접 띄우고 운영하지 않아도, 특정 이벤트가 발생했을 때 백엔드 코드를 실행할 수 있습니다.

처음 시작할 때는 아래 4단계로 이해하면 가장 쉽습니다.

1. Cloud Functions 설정
2. 함수 작성
3. 함수 테스트
4. 배포 및 모니터링

이 글에서는 이 흐름을 기준으로 Firebase Cloud Functions를 시작하는 방법을 정리합니다.

## 1. Cloud Functions 설정

가장 먼저 해야 할 일은 **Firebase CLI를 설치하고, 현재 Firebase 프로젝트에서 Cloud Functions를 초기화하는 것**입니다.
CLI는 함수 생성, 로컬 실행, 배포까지 전 과정을 담당하는 기본 도구입니다.

전역 설치는 다음처럼 진행합니다.

```bash
npm install -g firebase-tools
```

설치가 끝났으면 로그인합니다.

```bash
firebase login
```

그다음 Firebase 프로젝트 루트에서 Cloud Functions를 초기화합니다.

```bash
firebase init functions
```

초기화 과정에서는 보통 아래 항목들을 선택하게 됩니다.

- 어떤 Firebase 프로젝트에 연결할지
- JavaScript, TypeScript, Python 중 어떤 언어를 사용할지
- 필요한 기본 설정과 의존성 설치 여부

처음 입문하는 경우라면 JavaScript로 빠르게 구조를 익히는 방법이 가장 단순합니다.
반대로 타입 안정성과 유지보수를 더 중요하게 본다면 TypeScript가 더 적합합니다.
Python을 이미 업무나 데이터 처리 쪽에서 많이 쓰고 있다면 Python 기반으로 시작해도 됩니다.

초기화가 끝나면 일반적으로 `functions/` 폴더가 생성되고, 이 폴더 안에서 함수 코드를 작성하게 됩니다.
즉, 이 단계는 "Cloud Functions를 배포할 수 있는 기본 작업 공간을 만드는 과정"이라고 보면 됩니다.

## 2. 함수 작성

설정이 끝났다면 이제 실제로 동작할 함수를 작성합니다.
Cloud Functions는 크게 두 가지 방식으로 많이 사용됩니다.

- HTTP 요청을 받아 직접 호출되는 함수
- Firebase 또는 Google Cloud 이벤트에 반응하는 트리거 함수

예를 들어 다음과 같은 작업을 만들 수 있습니다.

- Firestore 문서 생성 시 데이터 후처리
- 회원 가입 직후 사용자 기본 정보 생성
- Cloud Storage 업로드 파일 처리
- 외부 API 호출 후 결과 저장
- 간단한 백엔드 API 엔드포인트 제공

JavaScript나 TypeScript를 선택했다면 보통 `functions/index.js` 또는 관련 소스 파일에 코드를 작성합니다.
Python을 선택했다면 `functions/main.py`에서 작성하게 됩니다.

이 단계에서 중요한 것은 "어떤 이벤트를 기준으로 함수를 실행할지"를 먼저 명확히 정하는 것입니다.
예를 들어 브라우저나 앱에서 직접 호출하고 싶다면 HTTP 함수가 맞고, Firestore 데이터 변경에 자동 반응해야 한다면 Firestore 트리거 함수가 맞습니다.

처음에는 너무 많은 기능을 한 번에 넣기보다, 작은 함수 하나로 시작하는 것이 좋습니다.
예를 들어 아래처럼 접근하면 진입 장벽이 낮습니다.

- HTTP 함수 하나 만들어 호출해 보기
- Firestore 문서 생성 이벤트 하나만 감지해 보기

이렇게 작게 시작하면 Cloud Functions의 실행 방식과 로그 흐름을 훨씬 빠르게 이해할 수 있습니다.

## 3. 함수 테스트

함수를 작성했다면 바로 배포하지 말고 **로컬 에뮬레이터로 먼저 테스트**하는 것이 좋습니다.
Firebase는 Local Emulator Suite를 제공하므로, 실제 운영 환경에 올리지 않고도 함수 동작을 검증할 수 있습니다.

에뮬레이터 실행은 보통 다음 명령으로 시작합니다.

```bash
firebase emulators:start
```

실행 후에는 로컬 주소와 Emulator UI 정보가 출력됩니다.
HTTP 함수라면 브라우저나 API 테스트 도구로 직접 호출해볼 수 있고, Firestore 트리거 함수라면 문서를 생성하거나 수정했을 때 함수가 예상대로 실행되는지 확인할 수 있습니다.

로컬 테스트 단계에서 확인해야 할 핵심은 아래와 같습니다.

- 함수가 원하는 시점에 실제로 실행되는가
- 입력값이 올바르게 처리되는가
- Firestore나 다른 Firebase 서비스에 기대한 결과가 저장되는가
- 에러 로그나 경고가 발생하지 않는가

이 단계를 건너뛰고 곧바로 배포하면, 단순한 설정 실수나 데이터 처리 오류를 운영 환경에서 발견하게 될 가능성이 높습니다.
초기 개발 단계일수록 에뮬레이터 테스트가 시간을 아껴 줍니다.

## 4. 배포 및 모니터링

테스트가 끝났다면 운영 환경으로 배포합니다.
여기서 먼저 알아둘 점은, **Cloud Functions를 배포하려면 Firebase 프로젝트가 Blaze 요금제여야 한다는 점**입니다.
Blaze는 사용량 기반 과금 방식이므로, 실제 호출량과 리소스 사용량에 따라 비용이 달라집니다.

배포는 다음 명령으로 진행합니다.

```bash
firebase deploy --only functions
```

배포가 완료되면 HTTP 함수는 호출 가능한 URL이 제공되고, 이벤트 기반 함수는 해당 트리거 조건이 충족될 때 실제 환경에서 실행됩니다.

하지만 배포가 끝이라고 생각하면 안 됩니다.
Cloud Functions는 이벤트 기반으로 동작하기 때문에, 특정 입력값이나 특정 시점의 요청에서만 문제가 드러날 수 있습니다.
그래서 배포 이후에는 반드시 로그를 확인해야 합니다.

로그 확인과 검색은 **Google Cloud 콘솔**에서 진행할 수 있습니다.
운영 중에는 아래 항목을 자주 보게 됩니다.

- 함수 실행 성공 여부
- 에러 메시지와 실패 원인
- 비정상적으로 많은 호출 수
- 실행 시간 증가 여부
- 특정 이벤트에서 반복되는 오류 패턴

즉, 배포와 모니터링은 따로 떨어진 단계가 아니라 한 흐름으로 봐야 합니다.
함수를 올리는 것만큼, 문제를 빠르게 찾을 수 있는 로그 환경을 익히는 것도 중요합니다.

## 마무리

Firebase Cloud Functions는 "서버를 직접 관리하지 않고도 백엔드 로직을 실행할 수 있는 방법"이라는 점에서 매우 실용적입니다.
처음에는 어렵게 느껴질 수 있지만, 실제 시작 흐름은 생각보다 단순합니다.

정리하면 다음 순서입니다.

1. `firebase-tools`를 설치하고 `firebase init functions`로 프로젝트를 초기화한다.
2. JavaScript, TypeScript, Python 중 하나로 함수를 작성한다.
3. `firebase emulators:start`로 로컬에서 테스트한다.
4. Blaze 요금제로 업그레이드한 뒤 `firebase deploy --only functions`로 배포한다.
5. Google Cloud 콘솔에서 로그를 확인하며 운영 상태를 모니터링한다.

처음에는 HTTP 함수 하나, 또는 Firestore 트리거 함수 하나만 만들어도 전체 흐름을 이해하기에 충분합니다.
이후 익숙해지면 인증 이벤트 처리, 스케줄 작업, 외부 API 연동 같은 형태로 점차 확장해 나가면 됩니다.

공식 문서:

- [Cloud Functions for Firebase 문서](https://firebase.google.com/docs/functions)
- [Cloud Functions 시작 가이드](https://firebase.google.com/docs/functions/get-started)
