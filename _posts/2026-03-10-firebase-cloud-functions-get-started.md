---
title: "Firebase Cloud Functions 시작하기: 설정, 테스트, 배포 흐름 정리"
date: 2026-03-10
layout: single
categories: [firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Firebase를 사용하다 보면 어느 순간
"클라이언트 코드만으로는 처리하기 애매한 작업"이 생깁니다.
회원 가입 직후 사용자 정보를 정리하거나,
Firestore 문서가 생성될 때 후처리를 하거나,
외부 API를 호출해 결과를 저장해야 하는 경우가 대표적입니다.

이럴 때 사용하는 것이 **Cloud Functions for Firebase**입니다.
서버를 직접 띄우고 운영하지 않아도,
특정 요청이나 이벤트가 발생했을 때 백엔드 코드를 실행할 수 있습니다.

이 글에서는 코드를 어떻게 작성하는지보다,
**Firebase Cloud Functions를 어떤 순서와 명령어로 사용하게 되는지**
큰 흐름 위주로 정리합니다.

처음 시작할 때는 아래 5단계로 이해하면 가장 쉽습니다.

1. Firebase CLI 설치
2. Firebase 로그인 및 프로젝트 연결
3. Cloud Functions 초기화
4. 앱에서 Cloud Functions 라이브러리 추가
5. 로컬 에뮬레이터 테스트
6. Blaze 요금제 확인 후 배포 및 로그 확인

## 1. Firebase CLI 설치

Cloud Functions를 시작할 때 가장 먼저 필요한 것은
**Firebase CLI**입니다.

CLI는 함수 초기화, 로컬 실행, 배포까지
전체 과정을 담당하는 기본 도구입니다.

전역 설치는 아래 명령으로 진행합니다.

```bash
npm install -g firebase-tools
```

설치가 끝났다면 아래 명령으로 버전을 확인해 두면 좋습니다.

```bash
firebase --version
```

즉, 이 단계는
"Cloud Functions를 터미널에서 다룰 준비를 하는 과정"입니다.

## 2. Firebase 로그인 및 프로젝트 연결

CLI 설치가 끝났다면
먼저 Firebase 계정으로 로그인합니다.

```bash
firebase login
```

그다음 현재 어떤 프로젝트에 연결되어 있는지 확인하거나,
필요하면 프로젝트를 선택합니다.

```bash
firebase projects:list
```

```bash
firebase use --add
```

이 단계에서 중요한 점은
내가 작업 중인 로컬 폴더가 어떤 Firebase 프로젝트와 연결되는지
명확히 해 두는 것입니다.

여기서 프로젝트 연결이 틀리면
배포 대상도 엉뚱한 프로젝트가 될 수 있습니다.

## 3. Cloud Functions 초기화

프로젝트 연결이 끝났다면
이제 Cloud Functions 작업 공간을 초기화합니다.

```bash
firebase init functions
```

초기화 과정에서는 보통 아래 항목들을 선택하게 됩니다.

- 어떤 Firebase 프로젝트에 연결할지
- JavaScript, TypeScript, Python 중 어떤 언어를 사용할지
- 기본 설정과 의존성 설치 여부

초기화가 끝나면 일반적으로 `functions/` 폴더가 생성됩니다.
이 폴더가 Cloud Functions 코드를 관리하는 기본 작업 공간입니다.

여기서 이 글은 코드 작성법 자체를 다루지는 않지만,
초기화 이후에는 보통 아래 방향으로 함수를 운영하게 됩니다.

- HTTP 요청을 받아 직접 호출되는 함수
- Firestore, Auth, Storage 같은 이벤트에 반응하는 함수
- 스케줄 기반으로 주기 실행되는 함수

즉, 이 단계는
"Cloud Functions를 배포할 수 있는 프로젝트 구조를 만드는 과정"입니다.

## 4. 앱에서 Cloud Functions 라이브러리 추가

Cloud Functions를 배포하는 것과 별개로,
안드로이드 앱에서 이 함수를 호출하려면
앱 쪽에도 **Firebase Functions SDK** 의존성이 들어 있어야 합니다.

즉, 클라이언트에서 Callable Function 같은 함수를 호출할 계획이라면
`app/build.gradle` 또는 `app/build.gradle.kts`에
관련 라이브러리를 추가해야 합니다.

예를 들어 Android에서는 보통 Firebase BoM과 함께 아래 의존성을 넣습니다.

```gradle
implementation(platform("com.google.firebase:firebase-bom:최신버전"))
implementation("com.google.firebase:firebase-functions")
```

Groovy 문법을 쓰는 경우에도 개념은 같습니다.

```gradle
implementation platform("com.google.firebase:firebase-bom:최신버전")
implementation "com.google.firebase:firebase-functions"
```

그리고 의존성 추가 후에는
프로젝트를 동기화해 앱에서 Functions API를 사용할 수 있게 해야 합니다.

이 단계에서 확인할 포인트는 아래와 같습니다.

- 앱 모듈 Gradle 파일에 `firebase-functions`가 들어 있는지
- Firebase BoM을 쓰는 경우 버전 관리 방식이 일관된지
- 앱이 실제로 Callable Function 또는 HTTP 호출 흐름을 사용할 준비가 되었는지

즉, 이 단계는
"배포된 Cloud Functions를 앱에서 호출할 수 있게 클라이언트 의존성을 준비하는 과정"입니다.

## 5. 로컬 에뮬레이터로 테스트

함수를 준비했다면
바로 배포하지 말고 **로컬 에뮬레이터로 먼저 테스트**하는 것이 좋습니다.

Firebase는 Local Emulator Suite를 제공하므로
실제 운영 환경에 올리지 않고도 함수 동작을 검증할 수 있습니다.

에뮬레이터 실행은 보통 아래 명령으로 시작합니다.

```bash
firebase emulators:start
```

특정 서비스만 골라서 실행하고 싶다면
아래처럼 `--only` 옵션을 사용할 수 있습니다.

```bash
firebase emulators:start --only functions
```

로컬 테스트 단계에서 확인해야 할 핵심은 아래와 같습니다.

- 함수가 원하는 시점에 실제로 실행되는가
- 요청이나 이벤트 입력값이 올바르게 들어오는가
- Firestore나 다른 Firebase 서비스에 기대한 결과가 반영되는가
- 에러 로그나 경고가 발생하지 않는가

즉, 운영 배포 전에
명령어 기반으로 전체 흐름을 먼저 확인하는 단계라고 보면 됩니다.

## 6. 배포 전에 Blaze 요금제 확인

Cloud Functions를 실제 Firebase 프로젝트에 배포하려면
먼저 **Firebase 프로젝트가 Blaze 요금제여야 합니다.**

이 점은 초반에 많이 놓치는데,
Spark 요금제 상태에서는 Cloud Functions 배포나 운영에 제한이 걸릴 수 있습니다.

Blaze는 사용량 기반 과금 방식입니다.
즉, 호출 수, 실행 시간, 네트워크 사용량 등에 따라 비용이 달라질 수 있습니다.

그래서 배포 전에 아래 두 가지를 같이 확인하는 편이 좋습니다.

- 현재 프로젝트가 Blaze 요금제로 전환되어 있는지
- 어떤 함수가 비용에 영향을 줄 수 있는지

즉, Cloud Functions는 "배포 명령만 알면 끝"이 아니라
"요금제 조건과 운영 비용 구조까지 이해하고 써야 하는 기능"입니다.

## 7. Cloud Functions 배포

요금제 조건을 확인했다면
이제 운영 환경으로 배포합니다.

가장 기본적인 배포 명령은 아래와 같습니다.

```bash
firebase deploy --only functions
```

특정 함수만 배포하고 싶다면
아래처럼 함수 이름을 지정할 수 있습니다.

```bash
firebase deploy --only functions:myFunction
```

배포가 완료되면
HTTP 함수는 호출 가능한 URL이 제공되고,
이벤트 기반 함수는 해당 트리거 조건이 충족될 때 실제 환경에서 실행됩니다.

즉, 실무에서는 전체 함수를 매번 올리기보다
필요한 함수만 골라서 배포하는 방식도 자주 사용합니다.

## 8. 로그 확인과 운영 모니터링

배포가 끝났다고 해서 작업이 끝나는 것은 아닙니다.
Cloud Functions는 이벤트 기반으로 동작하기 때문에
특정 입력값이나 특정 시점의 요청에서만 문제가 드러날 수 있습니다.

그래서 배포 이후에는 반드시 로그를 확인해야 합니다.

Firebase CLI로 로그를 볼 때는 보통 아래 명령을 사용합니다.

```bash
firebase functions:log
```

필요하면 최근 로그를 기준으로
에러 패턴이나 반복 실패를 먼저 확인하는 식으로 접근하면 됩니다.

운영 중에는 아래 항목을 자주 보게 됩니다.

- 함수 실행 성공 여부
- 에러 메시지와 실패 원인
- 비정상적으로 많은 호출 수
- 실행 시간 증가 여부
- 특정 이벤트에서 반복되는 오류 패턴

더 상세한 검색과 필터링은
Google Cloud 콘솔 로그 탐색기에서 할 수 있습니다.

즉, 배포와 모니터링은 따로 떨어진 단계가 아니라
한 흐름으로 봐야 합니다.

## 마무리

Firebase Cloud Functions는
"서버를 직접 관리하지 않고도 백엔드 로직을 실행할 수 있는 방법"이라는 점에서 매우 실용적입니다.

처음에는 복잡해 보여도
실제 입문 흐름은 아래 명령어 기준으로 정리할 수 있습니다.

1. `npm install -g firebase-tools`
2. `firebase login`
3. `firebase projects:list`
4. `firebase use --add`
5. `firebase init functions`
6. 앱 Gradle에 `com.google.firebase:firebase-functions` 추가
7. `firebase emulators:start`
8. Blaze 요금제 확인
9. `firebase deploy --only functions`
10. `firebase functions:log`

즉, 처음에는
"함수를 어떻게 아주 정교하게 작성할까"보다
"Cloud Functions를 어떤 순서로 설정하고 테스트하고 배포할까"를 먼저 익히는 편이 훨씬 중요합니다.

공식 문서:

- [Cloud Functions for Firebase 문서](https://firebase.google.com/docs/functions)
- [Cloud Functions 시작 가이드](https://firebase.google.com/docs/functions/get-started)
- [Local Emulator Suite 문서](https://firebase.google.com/docs/emulator-suite)
