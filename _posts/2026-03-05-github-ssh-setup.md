---
title: "GitHub 원격 저장소 연결: SSH 키 생성과 키체인 사용"
date: 2026-03-05
layout: single
categories: [git]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

GitHub 원격 저장소를 **SSH 방식**으로 연결하는 방법을 정리했습니다.  
핵심은 **SSH 키 생성 → GitHub에 등록 → 키체인에 저장** 순서입니다.

## 1. SSH 키 생성

먼저 새 키를 생성합니다. 이메일은 GitHub 계정 이메일로 바꾸세요.

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- 기본 경로: `~/.ssh/id_ed25519`
- 비밀번호(passphrase) 입력을 권장

## 2. SSH 에이전트 실행

```bash
eval "$(ssh-agent -s)"
```

## 3. 키를 키체인에 저장(macOS)

macOS는 키체인을 써두면 재부팅 후에도 편합니다.

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

만약 위 옵션이 안 된다면(구버전), 아래도 시도해 보세요:

```bash
ssh-add -K ~/.ssh/id_ed25519
```

## 4. GitHub에 공개키 등록

공개키를 복사합니다.

```bash
cat ~/.ssh/id_ed25519.pub
```

GitHub에서 **Settings → SSH and GPG keys → New SSH key**에 붙여넣고 저장합니다.

## 5. SSH 연결 테스트

```bash
ssh -T git@github.com
```

성공 시 “Hi <username>!” 메시지가 뜹니다.

## 6. 원격 URL을 SSH로 변경

이미 HTTPS로 연결된 저장소라면 이렇게 바꿉니다.

```bash
git remote -v
git remote set-url origin git@github.com:USER/REPO.git
```

---

### 자주 겪는 문제

- **권한 오류가 날 때**  
  `ssh -T git@github.com`로 먼저 인증 확인
- **여러 키를 쓰는 경우**  
  `~/.ssh/config`에 호스트별 키 설정을 추가하는 것이 안전
