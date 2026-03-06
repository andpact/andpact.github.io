---
title: "Firestore Security Rules 문법 정리: match, allow, request.auth"
date: 2026-03-06
layout: single
categories: [firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Firestore Security Rules는 클라이언트 코드와 별개로, 서버에서 최종 접근 권한을 검증하는 정책입니다.
이 글은 특정 서비스 규칙 예시가 아니라, 실제 작성에 필요한 **문법과 구조**를 중심으로 정리합니다.

## 1. 기본 구조

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // 규칙 작성 영역
  }
}
```

- `rules_version = '2';`: Firestore 규칙 문법 버전 선언
- `service cloud.firestore`: Firestore에 적용되는 규칙 블록
- `match /databases/{database}/documents`: Firestore 문서 경로의 루트

## 2. match 문법: 경로 지정

`match`는 "어떤 경로에" 규칙을 적용할지 정합니다.

```js
match /profiles/{uid} {
  // uid는 경로 변수
}
```

- `{uid}`처럼 중괄호로 선언한 값은 **경로 변수**
- 요청 경로의 실제 값이 변수에 바인딩됨

하위 전체를 한 번에 다루려면 재귀 와일드카드를 사용합니다.

```js
match /projects/{projectId}/{docPath=**} {
  // projectId 아래의 모든 하위 문서 경로에 매칭
}
```

- `{docPath=**}`: 깊이에 상관없이 하위 문서 전체 매칭

## 3. allow 문법: 동작 권한 지정

```js
allow read, write: if <조건식>;
```

- `read`: `get`, `list` 요청 포함
- `write`: `create`, `update`, `delete` 요청 포함

필요하면 동작별로 분리할 수 있습니다.

```js
allow create: if <조건식>;
allow update: if <조건식>;
allow delete: if <조건식>;
```

## 4. request/auth/resource 핵심 객체

Rules에서 가장 자주 쓰는 객체는 아래 4개입니다.

- `request.auth`: 인증 정보
- `request.resource.data`: 쓰기 요청으로 들어온 새 데이터
- `resource.data`: 현재 저장된 기존 데이터
- `request.time`: 요청 시각

예시:

```js
allow read: if request.auth != null;
```

- 로그인 사용자만 읽기 허용

```js
allow update: if request.auth != null
              && request.resource.data.ownerUid == resource.data.ownerUid;
```

- 기존 소유자 필드를 임의 변경하지 못하도록 방어

## 5. 자주 쓰는 조건식 패턴

### 5-1. 본인 데이터만 접근

```js
allow read, write: if request.auth != null
                   && request.auth.uid == uid;
```

### 5-2. 특정 필드 존재/타입 검증

```js
allow create: if request.resource.data.keys().hasAll(['title', 'createdAt'])
              && request.resource.data.title is string;
```

### 5-3. 업데이트 가능한 필드 제한

```js
allow update: if request.resource.data.diff(resource.data)
                .changedKeys()
                .hasOnly(['displayName', 'photoURL']);
```

## 6. 작성 시 주의할 점

- 기본값은 거부이므로 필요한 경로만 명시적으로 허용
- `match` 범위를 넓게 잡을수록 조건식을 더 엄격하게 작성
- `write`를 한 번에 열기보다 `create/update/delete` 분리 권장
- 클라이언트 검증만 믿지 말고 Rules에서 최종 검증

## 7. 운영 팁

- 배포 전 Rules Playground 또는 Emulator로 시나리오 테스트
- "로그인 안 함 / 본인 / 타인" 케이스를 최소 세트로 검증
- 컬렉션 구조 변경 시 규칙도 함께 수정하는 습관 필요

## 8. create/update/delete 분리 템플릿

아래는 문서 생성/수정/삭제 권한을 분리해서 관리하는 기본 템플릿입니다.

```js
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /posts/{postId} {
      // 읽기는 공개
      allow read: if true;

      // 생성은 로그인 사용자만 가능, 작성자 UID는 본인으로 고정
      allow create: if request.auth != null
                    && request.resource.data.ownerUid == request.auth.uid
                    && request.resource.data.keys().hasAll(['title', 'ownerUid', 'createdAt']);

      // 수정은 작성자만 가능, ownerUid는 변경 불가
      allow update: if request.auth != null
                    && resource.data.ownerUid == request.auth.uid
                    && request.resource.data.ownerUid == resource.data.ownerUid;

      // 삭제는 작성자만 가능
      allow delete: if request.auth != null
                    && resource.data.ownerUid == request.auth.uid;
    }
  }
}
```

핵심 포인트:

- `create`: 필수 필드 존재 검증 + 작성자 UID 강제
- `update`: 작성자 본인만 수정 + 소유자 필드 불변
- `delete`: 작성자 본인만 삭제

---

Firestore Security Rules는 길게 쓰는 것보다, 경로와 조건을 정확히 좁혀 쓰는 것이 핵심입니다.
상황별로 `create/update/delete`를 분리하면 권한 실수를 크게 줄일 수 있습니다.
