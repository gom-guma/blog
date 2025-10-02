---
title: GitHub Copilot ‘잘’ 사용하는 법 (1)
published: 2025-10-02
description: 커스텀 지침 설정을 통해 Copilot 품질을 향상시키는 방법
image: ./copilot_instruction-guide.png
category: AI
tags: [Copilot, AI, 프로그래밍 생산성, LLM]
---

# GitHub Copilot ‘잘’ 사용하는 법 (1)

`.github/copilot-instruction.md`, `prompt.md` 를 활용한 문서 기반 설정

## 1. Copilot이 항상 유용한 건 아니다

GitHub Copilot은 AI 기반 코드 생성 도구로, 주석이나 함수 시그니처에 기반하여 코드의 나머지 부분을 자동으로 제안해줍니다.

단순한 CRUD 작업이나 반복적인 패턴에서는 상당한 생산성 향상을 기대할 수 있지만, 프로젝트 규모가 커지거나 팀 고유의 코드 스타일이 자리잡은 경우에는 오히려 혼란을 야기할 수 있습니다.

Copilot은 사용자의 코드 문맥(context)을 기반으로 다음에 나올 코드를 예측하지만, 그 문맥은 매우 제한적입니다. 특히 다음과 같은 문제를 겪는 경우가 많습니다.

- 팀의 상태 관리 라이브러리는 Zustand인데 Redux 관련 코드가 제안됨
- 함수형 스타일로 작성된 컴포넌트에 class component 코드가 등장
- 네이밍 규칙(kebab-case, PascalCase 등)이 프로젝트 컨벤션과 다름

이런 문제는 단순히 Copilot을 끄는 것보다, **Copilot이 올바른 예측을 할 수 있도록 '지시문'을 주는 방식**으로 접근하는 것이 훨씬 효과적입니다.

GitHub에서는 이를 위해 **문서 기반 Instruction 시스템**을 제공하고 있으며, 이번 글에서는 `.github/copilot-instruction.md`와 `prompt.md`를 통해 이를 설정하는 방법을 설명합니다.

---

## 2. Instruction 기반 설정이란?

GitHub Copilot은 단순한 IDE 확장 수준을 넘어서, **프로젝트 내 특정 디렉토리의 문서 구조를 학습하여 코드 제안 품질을 향상**시키는 기능을 갖추고 있습니다.

이는 Copilot for PR, Copilot Workspace 등에서도 사용되는 메커니즘으로, Instruction 파일을 통해 다음과 같은 정보를 제공할 수 있습니다.

- 이 프로젝트는 어떤 기술 스택을 사용하는가?
- 어떤 코딩 컨벤션을 따르는가?
- 특정 디렉토리/파일은 어떤 역할을 하는가?
- 어떤 패턴은 지양해야 하는가?

이를 문서로 명시하면, Copilot은 해당 프로젝트의 전반적인 문맥을 보다 정밀하게 파악할 수 있으며, 코드 제안의 정확도도 자연스럽게 높아집니다.

---

## 3. 기본 구조

Copilot은 `.github/copilot/` 디렉토리 내에 존재하는 특정 파일들을 인식합니다. 기본적으로 아래와 같은 구성을 사용합니다.

```
.github/
└── copilot/
    ├── copilot-instruction.md
    └── prompt.md
```

| 파일명 | 설명 |
|--------|------|
| `copilot-instruction.md` | 프로젝트 전역에 대한 Copilot 지시문 |
| `prompt.md` | 특정 폴더나 도메인에 대한 보조 설명 |

---

## 4. copilot-instruction.md 작성 예시

> [!TIP]
> github-copilot 에서 제안하는 [5가지 팁](https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/)을 참고하세요.

Instruction 파일은 명확하고 단정적인 문체로 작성하는 것이 좋습니다. 주관적 판단이나 애매한 표현보다는, 규칙 또는 정책에 가까운 서술이 더 효과적으로 작동합니다.

```md
# Copilot Instruction

## 기술 스택
- React (v18)
- TypeScript
- Zustand
- Vite + Module Federation

## 코딩 규칙
- 모든 함수는 arrow function 형태로 작성
- 파일 이름은 kebab-case 사용
- 컴포넌트는 PascalCase로 작성
- UI 컴포넌트는 shadcn/ui 기반으로 작성

## 금지 사항
- class component 사용 금지
- Redux, Recoil 사용 금지
- Inline style 사용 금지

## 주석 스타일
- 코드 상단에 영어 주석 작성
- 추가 설명이 필요한 경우 한글 병기 허용
```

---

## 5. prompt.md 작성 예시

Copilot은 프로젝트 전체에 적용되는 설정 외에도, 디렉토리/도메인 수준에서 개별적인 문맥을 제공받을 수 있습니다. 이는 `prompt.md`를 통해 설정할 수 있으며, 디렉토리 구조와 역할을 간결하게 설명하는 것이 좋습니다.

```md
# Prompt 설정

- `src/features/auth`: 로그인, 회원가입, 비밀번호 찾기 기능을 담당합니다.
- `src/entities/user`: 사용자 모델, 유틸, 서버 타입 정의를 포함합니다.
- `src/shared/ui`: Button, Input 등 재사용 가능한 UI 컴포넌트를 정의합니다.
```

---

## 6. 적용 결과

회사에서 진행한 프로젝트에서 실제로 해당 설정을 적용해 보았습니다. 적용 전후 Copilot의 코드 제안 품질에는 다음과 같은 차이가 있었습니다.

| 항목 | 적용 전 | 적용 후 |
|------|---------|---------|
| 상태 관리 추천 | `useState`, `Redux` 추천 | `useStore` 기반 상태 추천 |
| 컴포넌트 스타일 | `function Comp()` 제안 | `const Comp = () => {}` 추천 |
| 불필요한 라이브러리 | Recoil import 등장 | 사용하지 않음 |
| 파일 생성 위치 | 루트 폴더에 생성 | 적절한 feature 폴더에 생성 |

---

## 7. 주의사항

- 이 설정은 Copilot의 **Enterprise, Workspace, Chat** 기능과 결합할 때 가장 큰 효과를 발휘합니다.
- 무료 사용자에게도 일부 기능은 적용되지만, 정확도나 적용 범위는 제한될 수 있습니다.
- Instruction 반영에는 수 분에서 수 시간이 소요될 수 있으며, 변경 즉시 반영되지는 않습니다.
- 문서가 과도하게 길거나, 두서없이 작성되면 오히려 역효과가 날 수 있습니다.

---

## 8. 마무리

GitHub Copilot은 생산성을 높이는 강력한 도구지만, 아무 설정 없이 사용하는 것은 미숙한 팀원이 프로젝트에 무작위 코드를 넣는 것과 크게 다르지 않습니다.  
`.github/copilot-instruction.md`와 `prompt.md`는 단순한 설정 파일을 넘어, **AI에게 우리 프로젝트의 철학과 컨벤션을 전달하는 인터페이스**입니다.

단 몇 줄의 문서 작성만으로도 더 나은 코드 추천을 받을 수 있으며, 장기적으로는 코드 품질 유지와 신규 팀원 온보딩에도 큰 도움이 될 수 있습니다.
