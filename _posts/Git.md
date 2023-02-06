---
title: "[Interview] Git"
excerpt: "Git 정리"

categories:
  - Interview
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-11-01
last_modified_at: 2022-11-01
---

개인적인 의견이 포함되어 있고, 잘못된 정보가 있을 수 있습니다.

## **Git**
- 프로젝트의 변경사항을 추적하여 버전을 관리하는 시스템

## **GitHub**
- Git을 사용하는 프로젝트를 저장하는 원격 저장 공간

## **Repository**
- Git으로 관리하는 프로젝트의 저장소
- Local Repository : 사용자의 컴퓨터에 위치한 저장소
- Remote Repository : 사용자 컴퓨터 외부에 위치한 저장소이며, 코드를 공유할 수 있다.

## **Commit**
- 프로젝트의 변경된 부분으로 새 버전을 만드는 행위

## **Branch**
- 독립적인 작업을 수행하기 위한 프로젝트의 복사본

## **PR(Pull Request)**
- 자신이 작업한 Branch를 Main Branch에 반영해달라는 요청, PR이 승인되면 자신이 작업한 내용이 Main Branch에 병합되게 된다.

## **Conflict**
- Main Branch가 여러 Branch와의 병합을 시도할 때, 어떤 Branch의 변경사항을 병합해야 할지 결정하지 못하는 상태

## **Git Merge와 Rebase**
- Git Merge : 한 Branch를 다른 Branch와 병합
- Git Rebase : 한 Branch의 Base Branch를 변경

## **Squash와 Reward**
- Squash : 여러 Commit을 하나의 Commit으로 뭉치기
- Reward : Commit 메시지를 수정