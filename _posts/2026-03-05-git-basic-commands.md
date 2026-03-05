---
title: "Git 기본 명령어 빠르게 정리"
date: 2026-03-05
layout: single
categories: [git]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Git을 처음 시작할 때 꼭 쓰게 되는 기본 명령어를 간단히 정리했습니다. 예시는 로컬 저장소 기준입니다.

## 1. 시작하기

- `git init`: 현재 폴더를 Git 저장소로 초기화
- `git clone <URL>`: 원격 저장소 복제

```bash
git init
git clone https://github.com/user/repo.git
```

## 2. 상태 확인

- `git status`: 변경 사항 요약
- `git log`: 커밋 기록 확인

```bash
git status
git log --oneline
```

## 3. 변경 사항 준비/저장

- `git add <파일>`: 스테이징에 추가
- `git add .`: 현재 폴더 변경 사항 전부 추가
- `git commit -m "메시지"`: 커밋 생성

```bash
git add README.md
git add .
git commit -m "docs: update README"
```

## 4. 변경 내용 확인

- `git diff`: 스테이징 전 변경 확인
- `git diff --staged`: 스테이징된 변경 확인

```bash
git diff
git diff --staged
```

## 5. 브랜치

- `git branch`: 브랜치 목록
- `git branch <이름>`: 브랜치 생성
- `git switch <이름>`: 브랜치 이동
- `git switch -c <이름>`: 생성 후 이동

```bash
git branch
git branch feature/login
git switch feature/login
git switch -c fix/typo
```

## 6. 원격 저장소

- `git remote -v`: 원격 저장소 확인
- `git push`: 원격에 업로드
- `git pull`: 원격 변경 가져오기 + 병합

```bash
git remote -v
git push origin main
git pull origin main
```

## 7. 되돌리기(기초)

- `git restore <파일>`: 워킹 트리 변경 되돌리기
- `git restore --staged <파일>`: 스테이징 해제
- `git commit --amend`: 마지막 커밋 수정

```bash
git restore index.html
git restore --staged index.html
git commit --amend
```

---

### 주의

- `git pull`은 현재 브랜치에 병합을 수행합니다. 변경 사항이 많다면 먼저 `git status`로 확인하는 습관이 좋습니다.
- 커밋 메시지는 목적이 드러나게 간결하게 작성하세요.
