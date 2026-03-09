---
title: "UserInterfaceState.xcuserstate 파일이란?"
date: 2026-03-09
layout: single
categories: [xcode]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Xcode 프로젝트를 쓰다 보면 `UserInterfaceState.xcuserstate` 파일이 자주 생깁니다.
이 파일은 코드나 빌드 설정이 아니라, **개인 작업 화면 상태**를 저장하는 파일입니다.

## 한 줄 정의

`UserInterfaceState.xcuserstate`는
Xcode가 "어떤 파일을 열어뒀는지, 에디터 UI 상태가 어땠는지"를 복원하기 위해 쓰는
**사용자별 UI 상태 파일**입니다.

## 주로 어떤 정보가 들어가나?

- 마지막으로 열어둔 파일/탭 정보
- 에디터 분할, 스크롤, 포커스 같은 UI 상태
- 개인 로컬 환경에서만 의미 있는 편집기 상태

즉, 팀원이 공유받아야 할 프로젝트 설정 정보가 아니라
**내 Mac에서의 작업 편의 정보**에 가깝습니다.

## 파일 위치

보통 아래 경로 형태에 생성됩니다.

- `YourProject.xcodeproj/project.xcworkspace/xcuserdata/<username>.xcuserdatad/UserInterfaceState.xcuserstate`

워크스페이스를 쓰는 경우에도 유사하게 `xcuserdata/` 아래에 생성됩니다.

## Git에 커밋해야 할까?

일반적으로는 **커밋하지 않는 것이 맞습니다.**

- 사용자별 상태라서 협업 가치가 거의 없음
- 커밋하면 불필요한 변경/충돌이 자주 발생
- `.gitignore`에서 `*.xcuserstate` 또는 `xcuserdata/`를 제외하는 것이 일반적

## 왜 자주 변경되나?

Xcode를 열고 닫거나, 파일 탭을 바꾸거나, 창 레이아웃이 바뀔 때마다
상태가 갱신될 수 있습니다.

그래서 코드 변경이 없어도 Git 변경 목록에 자주 나타날 수 있습니다.

## 정리

`UserInterfaceState.xcuserstate`는
프로젝트 로직 파일이 아니라 **개인 UI 상태 캐시 파일**입니다.

협업 저장소에서는 보통 추적하지 않고,
`.gitignore`로 제외해 노이즈를 줄이는 것이 실무적으로 안전합니다.
