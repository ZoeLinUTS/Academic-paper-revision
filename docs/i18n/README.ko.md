# 학술 논문 개정 Skill

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | [日本語](README.ja.md) | **한국어** | [Español](README.es.md) | [Français](README.fr.md) | [Deutsch](README.de.md) | [Português](README.pt.md)
<!-- 번역은 커뮤니티가 관리합니다. 영어가 기준이며 더 앞서 있을 수 있습니다. -->

---

거칠거나 학생 스타일인 연구 논문 초안(LaTeX)을 **특정 투고처**에서 *채택된* 논문처럼 읽히는
원고로 바꿔 주는 재사용 가능한 **Claude skill**입니다. 이 skill은 *투고처 우선*입니다. 먼저
대상 투고처의 규칙을 파악하고, 그 학회의 채택 논문을 연구한 뒤 다시 씁니다. 특정 학회에는 종종
맞지 않는 일반적인 "좋은 글쓰기" 조언을 적용하지 않습니다.

> **상태:** skill과 합성 샘플 초안은 지금 바로 사용할 수 있습니다. 호스팅되는 읽기 전용 PDF
> 리뷰어(Vercel / GitHub Pages)는 [로드맵](#로드맵)에 있습니다.

## 이 저장소의 구성

```
SKILL.md                            skill 본체(Claude가 따르는 지침)
references/venue-conventions.md     학회 관례(ACL 계열, LNCS/AJCAI, ML 학회)
docs/USER-GUIDE.md                  단계별 사용법, 두 가지 모드, FAQ
docs/i18n/                          번역된 README(상단 언어 스위처)
examples/student-draft/             합성된 분량 부족 초안 + .bib + 정답지
examples/over-length/               합성된 분량 초과 초안(삭감 연습)
```

## skill이 하는 일

- **투고처 우선 관례.** 원고에 손대기 전에 대상 투고처의 규칙(페이지 제한, 필수 섹션, 익명성,
  템플릿)을 불러옵니다. 예를 들어 ACL 계열의 Limitations/Ethics는 *무료*(제한에 포함되지 않음)
  이고, LNCS는 future work를 conclusion에 접어 넣는다는 것을 압니다.
- **채택 논문의 어조.** RQ/RO 스캐폴딩을 걷어내고 산문형 기여로 바꾸며, 서술형 명사구 제목을
  쓰고, 관련 연구를 시간순이 아니라 주제별로 재구성합니다.
- **균형 잡힌 형식화.** 방법이 덜 형식화된 곳에 번호가 매겨진 수식과 `algorithm` 플로트를
  추가하고, 각 수식에는 오독을 막는 설명을 덧붙입니다.
- **구조·상호 참조 수정.** 참조되지 않은 그림을 연결하고, 매달린 `\ref`/`\cite`를 해결하며,
  누락된 프리앰블 패키지를 보충합니다.
- **페이지 맞춤.** 내용을 보존하는 편집(단락 병합, 예시를 부록으로 이동)으로 본문을 제한 안에
  맞춥니다.
- **참신성과 위치 설정.** 요청 시 해당 연구 계보를 재구성하고, 선행 연구를 주제별로 묶고,
  차별점을 짚어 내며, 예상되는 심사자 공격 지점을 솔직하게 나열합니다.

## 두 가지 사용 방식

1. **전체 개정(당신의 LaTeX을 편집).** 컴파일 가능한 소스(`.tex` + `.bib` + 클래스/스타일 파일
   + `figures/`, 또는 Overleaf 내보내기 zip)를 주면 외과적 편집을 하고, 늘어난 분량을 상쇄한 뒤,
   재구성한 업로드용 zip을 돌려줍니다. **결과 수치를 결코 지어내지 않습니다.**
2. **리뷰 전용(편집 없음).** Claude가 초안을 읽고 *개요와 코멘트만* 돌려줍니다——연구 주제/범위,
   핵심 기여, 솔직한 참신성 평가, 문제점, 구체적 제안——파일은 전혀 바꾸지 않습니다. 계획 중인
   웹 앱이 노출할 모드가 바로 이것입니다.

두 모드 모두 **어떤 언어로도** 작동합니다. 중국어, 스페인어, 아랍어 등 어떤 언어로든 질문하고
개요·코멘트·요약을 받을 수 있습니다. *대화*만 당신의 언어를 따르고, 원고 자체는 학회가 요구하는
언어(보통 영어)로 유지됩니다.

자세한 안내는 [`docs/USER-GUIDE.md`](../USER-GUIDE.md)를 참고하세요.

## 빠른 시작

이 skill을 포함한 저장소에서 Claude(예: Claude Code)로 사용할 때:

1. Claude에게 논문 소스(또는 아래 샘플)와 **대상 학회**를 알려 줍니다.
2. *"이걸 <학회>용으로 개정해 줘"*(전체 모드) 또는 *"이 초안만 리뷰해서 개요와 코멘트를 줘,
   아무것도 편집하지 마"*(리뷰 전용 모드)라고 요청합니다.
3. Claude는 먼저 **오리엔테이션 브리핑**과 **발견 목록**을 돌려주고, 그다음——전체 모드에서는——
   편집을 하고 무엇을 다시 컴파일하고 확인해야 하는지 정확히 알려 줍니다.

포함된 합성 초안으로 시험해 보세요(둘 다 **가상**——가상의 저자·데이터셋·수치, IP 문제 없음——
각각 `EXPECTED-FINDINGS.md` 정답지 포함):

```
examples/student-draft/main.tex   분량 부족; 전형적인 학생 초안 문제
examples/over-length/main.tex     페이지 제한 초과; 삭감 연습
```

- **`student-draft`**는 RQ1–RQ3 스캐폴딩, 일반적인 제목, 시간순 관련 연구, 수식 0개, `\label`이
  없는 그림에 대한 `\ref`를 심어 두었습니다. 제한 *미만*이라 무료 섹션 추가를 연습합니다.
- **`over-length`**는 ACL 쇼트 페이퍼(4페이지) 제한이 대상이지만 약 5~6페이지입니다. 다른 면은
  다듬어져 있어 **삭감 단계**(중복 단락 병합, 예시를 부록으로 이동)를 연습합니다.

## 로드맵

호스팅되는 **읽기 전용 PDF 리뷰어**: 사용자가 PDF 초안을 업로드하면 개요와 코멘트——연구 주제와
범위, 다루는 문제, 핵심 기여, 솔직한 참신성 평가, 개선 제안——를 받습니다(**LaTeX 편집은 전혀
없음**). 위의 리뷰 전용 모드 위에 올리는 작은 웹 프런트엔드(Vercel 또는 GitHub Pages)로 계획
중입니다. 같은 저장소에서 추적하는 별도 작업이며, 분석 로직은 이미 skill 안에 있습니다.

## 입력에 대한 참고

전체 개정에는 **PDF가 아니라 LaTeX 소스**가 필요합니다. PDF는 외과적으로 편집할 수 없고, PDF에서
소스를 재구성하면 저자의 서식과 수치가 손상될 위험이 있기 때문입니다. 리뷰 전용 모드는 읽고
코멘트만 하므로 PDF로도 작동합니다.
