---
title: "터미널 기본 키워드(cd, ls, pwd 등) 빠르게 정리"
date: 2026-03-04
layout: single
categories: [os]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

터미널에서 자주 쓰는 기본 명령어를 짧게 정리했습니다. 예시는 macOS/Linux 기준입니다.

## 1. 위치/이동

- `pwd`: 현재 위치 출력
- `ls`: 현재 폴더 목록 보기
- `cd <경로>`: 경로로 이동

```bash
pwd
ls
cd /Users/you/projects
cd ..
cd ~
```

## 2. 파일/폴더 다루기

- `mkdir <폴더>`: 폴더 생성
- `touch <파일>`: 빈 파일 생성
- `cp <원본> <대상>`: 복사
- `mv <원본> <대상>`: 이동/이름변경
- `rm <파일>`: 파일 삭제
- `rm -r <폴더>`: 폴더 삭제(주의)

```bash
mkdir logs
touch README.md
cp README.md README.backup.md
mv README.backup.md docs.md
rm docs.md
rm -r logs
```

## 3. 내용 확인

- `cat <파일>`: 파일 전체 출력
- `less <파일>`: 스크롤 가능한 보기
- `head -n 20 <파일>`: 앞부분 20줄
- `tail -n 20 <파일>`: 끝부분 20줄

```bash
cat README.md
less README.md
head -n 20 README.md
tail -n 20 README.md
```

## 4. 검색/필터

- `grep "키워드" <파일>`: 키워드 포함 줄 찾기
- `find <경로> -name "패턴"`: 파일/폴더 찾기

```bash
grep "TODO" README.md
find . -name "*.md"
```

## 5. 도움말

- `man <명령어>`: 매뉴얼 보기
- `--help`: 간단 도움말

```bash
man ls
ls --help
```

## 6. 자주 쓰는 팁

- `clear`: 화면 정리
- `history`: 이전 명령어 기록
- `!!`: 바로 직전 명령어 재실행

```bash
clear
history
!!
```

---

### 주의

- `rm -r`는 복구가 어렵습니다. 삭제 전에 경로를 꼭 확인하세요.
- 중요한 파일은 `cp`로 백업한 뒤 작업하는 습관이 좋습니다.
