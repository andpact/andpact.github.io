---
title: "Firestore 문서 크기 제한(SHA 아님) 정리: 1 MiB 규칙"
date: 2026-03-05
layout: single
categories: [firebase]
toc: true
toc_label: "이 글의 내용"
toc_icon: "list"
toc_sticky: true
---

Firestore는 **문서(document) 크기에 하드 제한**이 있습니다. citeturn1search2
이 글은 공식 문서 기준의 크기 제한과 필드 타입을 정리합니다.

## 핵심 요약

- **문서 최대 크기:** 1 MiB (1,048,576 bytes) citeturn1search2
- **필드 값 최대 크기:** 1 MiB - 89 bytes (1,048,487 bytes) citeturn1search2
- 제한은 **하드 리밋**이며 초과 시 저장/업데이트가 실패합니다. citeturn1search2

## “문서 크기 1 MiB”가 의미하는 것

공식 문서 기준으로 **문서 최대 크기 1 MiB**가 하드 리밋입니다. citeturn1search2

## Firestore 문서 필드 타입

공식 문서에 따르면 Firestore는 아래 필드 타입을 지원합니다. citeturn5view0

- `Array`
- `Boolean`
- `Bytes`
- `Date and time`(Timestamp)
- `Floating-point number`
- `Geographical point`
- `Integer`
- `Map`
- `Null`
- `Reference`
- `Text string`
- `Vector`

추가로, **배열은 배열을 직접 포함할 수 없고**, **벡터는 최대 2048차원**까지 지원됩니다. citeturn5view0

## 체크리스트

- 문서 저장 실패 시 **1 MiB 초과 여부** 확인 citeturn1search2
- **필드 값 1 MiB - 89 bytes** 초과 여부 확인 citeturn1search2

---

### 참고

Firestore의 기타 제한(문서 ID 길이, 필드명 제한, 인덱스 크기 등)도 함께 확인하면 설계에 도움이 됩니다. citeturn1search2
