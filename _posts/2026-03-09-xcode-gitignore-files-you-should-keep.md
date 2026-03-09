---
title: "Xcode 프로젝트에서 .gitignore에 넣으면 안 되는 파일 정리"
date: 2026-03-09
layout: single
categories: [xcode]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Xcode 프로젝트를 Git으로 관리할 때,
`.gitignore`를 잘못 설정하면 팀원이 프로젝트를 열지 못하거나 빌드가 깨질 수 있습니다.

이번 글은 **".gitignore에 넣으면 안 되는 파일(=반드시 추적해야 하는 파일)"**에 집중해서 정리합니다.

## 핵심 요약

- `.xcodeproj` / `project.pbxproj`는 반드시 Git에 포함
- Swift Package Manager의 `Package.resolved`는 팀 재현성을 위해 보통 포함
- 소스코드, 리소스, 설정 plist 파일은 당연히 포함
- 반대로 `DerivedData`, 사용자별 workspace 데이터는 ignore

## .gitignore에 넣으면 안 되는 파일

아래 파일/폴더는 보통 **커밋 대상**입니다.

- `*.xcodeproj/project.pbxproj`
- `*.xcworkspace/contents.xcworkspacedata`
- `Package.swift` (SPM 사용 시)
- `Package.resolved` (팀/CI 버전 고정 필요 시)
- `Sources/`, `Tests/` 등 실제 코드 디렉터리
- `Assets.xcassets`, `*.storyboard`, `*.xib`
- `Info.plist`, `*.entitlements`

특히 `project.pbxproj`를 ignore하면,
파일 추가/타깃 설정/빌드 설정 변경이 팀에 공유되지 않습니다.

## 자주 하는 실수

### 1) `.xcodeproj` 전체를 ignore하는 경우

일부 템플릿을 그대로 복사하다가 `*.xcodeproj`를 통째로 제외하는 실수가 있습니다.
이 경우 팀원이 프로젝트 구조를 받을 수 없습니다.

### 2) `Package.resolved`를 무조건 ignore하는 경우

라이브러리 버전 재현이 중요한 앱/팀 프로젝트에서는
`Package.resolved`를 커밋하는 편이 안전합니다.

- 커밋 권장: 팀 개발, CI/CD, 릴리즈 안정성 중요
- ignore 가능: 개인 실험 프로젝트, 버전 고정 불필요

## 권장 .gitignore 예시 (Xcode)

아래 예시는 **ignore해야 할 것만** 담은 최소 안전 세트입니다.

```gitignore
# Xcode user data
xcuserdata/
*.xcuserstate

# Build artifacts
DerivedData/
build/

# SwiftPM local cache
.swiftpm/

# CocoaPods (팀 정책에 따라 선택)
# Pods/

# Carthage build outputs
Carthage/Build/

# macOS
.DS_Store
```

## 체크리스트

- `project.pbxproj`가 추적 중인지 확인
- `contents.xcworkspacedata`가 추적 중인지 확인
- `DerivedData/`, `xcuserdata/`는 ignore 처리
- `Package.resolved` 포함 여부를 팀 규칙으로 고정

## 마무리

Xcode에서 `.gitignore`는
"무엇을 뺄지"보다 **"무엇을 반드시 남길지"**를 먼저 정하는 게 안전합니다.

한 번만 제대로 정해두면,
프로젝트 설정 충돌과 팀 빌드 오류를 크게 줄일 수 있습니다.
