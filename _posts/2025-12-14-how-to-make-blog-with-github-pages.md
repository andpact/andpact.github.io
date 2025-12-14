---
title: "깃허브 페이지로 블로그(username.github.io) 생성하는 법"
date: 2025-12-14
layout: single
categories: [git]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

## 1. 레포지토리 생성

- GitHub에서 새 레포지토리를 생성합니다.  
- 이름은 `<username>.github.io` 형식으로 지정합니다.
- 퍼블릭 저장소를 선택하고, README를 추가하지 않습니다.

---

## 2. 로컬 폴더 준비

### 2-1. 로컬 폴더 생성

- **GUI 방법:** 탐색기(Windows)나 Finder(macOS)에서 새 폴더를 만듭니다. 원하는 위치에 `username.github.io` 폴더를 생성하세요.
- **터미널 방법:** 명령어로도 생성 가능합니다.

```bash
mkdir username.github.io
```

- `username` 부분은 실제 GitHub 계정 이름으로 변경하세요.
- 이 폴더가 로컬 블로그 작업의 기준 폴더가 됩니다.

### 2-2. 생성한 폴더로 이동

- GUI 방법: 폴더를 더블 클릭해서 열고, 터미널이나 명령 프롬프트에서 작업을 시작합니다.
- 터미널 방법: 다음 명령어를 사용합니다.

```bash
cd username.github.io
```

### 2-3. Git 저장소 초기화

- **GUI 방법**: Git GUI 클라이언트(예: GitHub Desktop)를 사용해서 새 저장소로 초기화할 수 있습니다.
- **터미널 방법**: 다음 명령어를 실행합니다.

```bash
git init
git branch -m main
```

- `git branch -m main`은 브랜치를 main으로 변경합니다. GitHub Pages는 main 브랜치 기준으로 자동 배포됩니다.

## 3. 첫 게시물 준비 및 업로드

### 3-1. _posts 폴더 생성

- **GUI 방법**: 로컬 블로그 폴더 안에 `_posts`라는 새 폴더를 만듭니다.
- **터미널 방법**:

```bash
mkdir _posts
```

### 3-2. 첫 게시물 작성

- **파일 생성 규칙**: `_posts/YYYY-MM-DD-포스트제목.md`
- **예시 터미널 명령어**:

```bash
touch _posts/2025-12-14-first-post.md
```

- **예제 Markdown 내용**:

```bash
---
title: "첫 글"
date: 2025-12-14
layout: single
---

안녕하세요. 이것은 첫 번째 게시물입니다.  
GitHub Pages + Jekyll + Minimal Mistakes 블로그 생성 테스트용 글입니다.
```

### 3-3. 로컬 Git 커밋

- **GUI 방법**: Git GUI나 GitHub Desktop에서 변경 사항을 스테이징하고 커밋합니다.
- **터미널 방법**:

```bash
git add .
git commit -m "Add first post"
```

### 3-4. 원격 저장소 연결 및 푸시

- **GUI 방법**: GitHub Desktop에서 `Publish repository` 또는 `Push origin` 버튼 클릭
- **터미널 방법x**:

```bash
git remote add origin https://github.com/username/username.github.io.git
git push -u origin main
```

- `-u` 옵션은 로컬 브랜치와 원격 브랜치를 추적하도록 설정합니다.
- 이후에는 단순히 `git push`만으로도 푸시가 가능합니다.

### 3-5. 사이트 확인

- 브라우저에서 https://username.github.io로 접속해 블로그와 첫 게시물을 확인합니다.