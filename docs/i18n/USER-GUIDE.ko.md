# 사용자 가이드

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | [日本語](USER-GUIDE.ja.md) | **한국어** | [Español](USER-GUIDE.es.md) | [Français](USER-GUIDE.fr.md) | [Deutsch](USER-GUIDE.de.md) | [Português](USER-GUIDE.pt.md)

`academic-paper-revision` skill을 어떻게 쓰는지, 무엇을 주는지, 무엇이 돌아오는지, 그리고 두 동작
모드가 어떻게 다른지.

## 1. 시작 전: 무엇을 준비할까

| 모드 | 제공하는 입력 | Claude가 하는 일 |
| --- | --- | --- |
| **전체 개정** | **LaTeX 소스**: 메인 `.tex`, `references.bib`, 학회 클래스/스타일(`llncs.cls` + `*.bst`, 또는 `acl.sty` …), `figures/`. Overleaf **프로젝트 내보내기(zip)**가 이상적. | 소스에 외과적 편집을 하고, 늘어난 분량을 상쇄하며, 재구성한 업로드용 zip을 돌려줌. |
| **리뷰 전용** | **PDF**(또는 소스)면 충분. | 읽고 코멘트만 함 — **파일을 변경하지 않음**. |

전체 개정에 소스가 필요한 이유는 PDF를 외과적으로 편집할 수 없고, PDF에서 소스를 재구성하면 서식과
수치를 잃을 위험이 있기 때문입니다. 렌더링된 `main.pdf`는 렌더링 시 속성(페이지 수, 플로트 배치)
확인에 유용한 *보조* 입력이지만, 그것만으로는 결코 충분하지 않습니다.

## 2. 대상 학회를 Claude에게 알리기

이것은 결정적입니다. 같은 편집이라도 한 학회에는 맞고 다른 학회에는 즉시 리젝이 될 수 있습니다.
학회명(ACL, NAACL, EMNLP, AJCAI, NeurIPS …)을 명시하세요. 학회의 정확한 규칙이 불분명하면 Claude가
편집 전에 최신 논문 모집 요강을 찾아봅니다. 파일명은 한 학회를, 메모는 다른 학회를 가리키면 그
모순을 짚어 물어봅니다 — 결코 넘겨짚지 않습니다.

## 3. 모드를 고르고 요청하기

**전체 개정** — 예시:
- "이걸 EMNLP용으로 개정해서 채택 논문처럼 읽히게 해줘."
- "이걸 AJCAI 페이지 제한에 맞춰 줄이고 RQ 스캐폴딩을 없애줘."
- "방법에 수식/알고리즘을 넣고 상호 참조를 고쳐줘."

**리뷰 전용** — 예시:
- "이 초안만 리뷰해줘 — 개요와 코멘트만, 아무것도 편집하지 마."
- "참신성을 평가하고 예상되는 심사자 반론을 알려줘."
- "여기 범위와 주요 문제는? 선행 연구와 어떻게 다른가?"

### 대화 언어 — 어떤 언어든

**어떤 언어로도** 작업하고 그 언어로 결과를 받을 수 있습니다. Claude는 *대화*를 어떤 언어로 할지
— 중국어, 스페인어, 아랍어 등 — 묻거나 추론하고, 모든 개요·코멘트·발견·요약을 그 언어로 전달합니다.
이는 원고와 별개입니다: **논문은 영어로**(또는 학회가 요구하는 언어) 유지되고, 대화만 당신이 고른
언어를 따릅니다.

## 4. 출력의 모습

두 모드 모두 같은 심사자 스타일 메모로 시작합니다:

1. **오리엔테이션 브리핑** — *초안이 무엇인가* · *핵심 기여* · *참신성(솔직, 심사자 공격 항목 포함)* ·
   *완성 상태*. 학회를 확인하고 원하는 범위를 물으며 마무리.
2. **발견 목록** — 문제마다 한 줄, 그룹화·심각도 순, 각 줄이 수정을 명시. 예:
   - `[venue]` 대상 = ACL (ARR): 본문 8페이지, Limitations/Ethics 무료, 이중 맹검.
   - `[structure]` 명시적 RQ1–RQ3 스캐폴딩 → 산문형 기여로 변환.
   - `[formalization]` 점수 규칙이 핵심인데 수식 0개 → 2–3개 수식 추가.
   - `[rigor]` 대표 성과에 유의성 검정 없음 / 단일 분할 → 최대 약점으로 지적.
   - `[bug]` `\ref{fig:pipeline}`가 `\label` 없는 그림을 가리킴 → label 추가.
   - `[cross-ref]` 관련 연구가 연대순 목록 → 주제별로 재구성.
   - `[page-fit]` +0.5페이지 초과 → 이를 상쇄하는 정확한 `[trim]` 조치와 짝지음
     (중복 단락 병합, 예시를 부록으로 이동).

**리뷰 전용은 여기서 끝**(구체적 제안 몇 가지와 솔직한 주의 하나 추가). 파일은 건드리지 않음.

**전체 개정은 계속:**

3. **변경 요약**(편집 후) — 무엇을 *왜* 바꿨는지, 순서대로: 적용한 학회 관례 · 프레이밍/구조 ·
   추가한 형식화 · 그림 · 빌드/상호 참조 수정 · 페이지 맞춤. 그다음 **당신이 해야 할 일**: 재컴파일
   (예: Overleaf에서), 지명된 렌더링 시 항목(페이지 수, 특정 글리프/플로트) 확인, 확인이 실패하면
   다음 구체적 방안. 로컬에서 확인 못 한 것과 미확인 학회 규칙을 표시함.
4. **솔직한 주의 하나** — 가장 큰 심사자 리스크를 솔직하게 진술.

## 4b. 실제 예시(입력 → 출력)

구체적으로, 포함된 `examples/student-draft/`에서의 실행은 이렇게 보입니다.

**예상 입력** — LaTeX 소스(PDF 아님)와 지정한 학회:

```
examples/student-draft/
  main.tex          # article 클래스 초안: RQ1–RQ3, 일반적 제목,
                    #   수식 0개, \label 없는 그림에 대한 \ref{fig:pipeline}
  references.bib    # 제공되지만 \cite되지 않음
```

> 프롬프트: *"이걸 **ACL (ARR)**용으로 리뷰한 뒤 채택 논문처럼 읽히게 개정해줘."*

**샘플 출력 — Claude가 먼저 돌려주는 브리핑 + 발견 목록:**

```
Orientation briefing
  What it is   : a sentiment classifier that keeps punctuation as tokens; 3 sections.
  Contribution : punctuation-aware tokenization improves accuracy on movie reviews.
  Novelty      : thin as written — the idea is plausible but reads as one ablation.
                 Reviewer attack: "this is a preprocessing tweak, where's the analysis?"
  Completeness : results present but no significance test; one figure renders "??".

Findings (severity-ordered)
  [venue]         Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
  [structure]     Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
  [formalization] Zero equations though the scoring rule is central → add 2–3 eqs.
  [rigor]         Headline gain has no significance test / single split → top weakness.
  [bug]           \ref{fig:pipeline} targets a figure with no \label → add the label.
  [cross-ref]     Related work is a dated list → regroup thematically.
  [venue]         No Limitations section — ACL requires it and it is page-free → add.
```

**리뷰 전용**은 거기서 끝납니다(구체적 제안 몇 가지와 솔직한 주의 하나 추가). **전체 개정**은 이어서
편집을 하고 무엇을 재컴파일하고 확인해야 하는지 정확히 알려주는 변경 요약을 돌려줍니다. 어떤 실행이든
샘플의 `EXPECTED-FINDINGS.md`에 대조해 채점할 수 있습니다.

## 5. Overleaf 왕복(전체 모드)

1. Overleaf에서: **Menu → Download → Source**(zip).
2. 그 zip(또는 압축 푼 소스)을 Claude에게 준다.
3. Claude가 소스를 편집하고 빌드 입력(`.tex`, `.bib`, 클래스/스타일, `figures/`)만 담은 업로드용
   zip을 재구성한다 — `main.pdf`, README, git 파일은 제외.
4. zip을 Overleaf에 다시 업로드해 재컴파일.
5. Claude가 지명한 렌더링 시 항목을 확인(페이지 수는 렌더링 시 속성이라 컴파일 후에만 확인 가능).

## 6. 연구 진실성 보장

- **수치를 결코 지어내지 않음.** 결과 자리표시자는 당신이 제공한 실제 저장된 실행에서만 채워집니다.
  결과가 아직 없으면 자리표시자로 남습니다.
- **당신의 기술 내용을 보존.** 편집은 외과적이며, 지적 내용은 남고 표현 방식만 바뀝니다.
- **요청하지 않은 공유 작업 없음.** 당신이 요청하지 않으면 commit, push, 전송을 하지 않습니다.

## 7. 샘플로 시험하기

두 개의 합성 초안이 포함됩니다(가상의 저자·데이터셋·수치 — IP 없음). 각각 실행을 채점할 수 있는
`EXPECTED-FINDINGS.md` 정답지가 있습니다:

- **`examples/student-draft/main.tex`** — 페이지 제한 *미만*, 전형적 문제를 심음: RQ1–RQ3 스캐폴딩,
  일반적 제목("Approach", "Experiment 1"), 연대순 관련 연구, 점수 규칙이 핵심인데 수식 0개, 그리고
  `\label` 없는 그림에 대한 `\ref{fig:pipeline}`(`??`로 렌더). 유의성 검정도 없음 — `[rigor]` 발견.
- **`examples/over-length/main.tex`** — ACL 쇼트 페이퍼(4페이지) 제한 대상이지만 약 5~6페이지. 다른
  면은 다듬어져 있어 **삭감 단계**를 연습: 중복 도입 단락 둘 병합, 반복적 관련 연구 압축, 긴 예시를
  부록으로 이동.

어느 초안에서든 어느 모드를 실행해 브리핑, 발견 목록, 그리고 — 전체 모드에서는 — 편집을 확인하세요.

## 8. FAQ

**PDF만 줘도 되나요?** 리뷰 전용은 됩니다. 전체 개정은 안 됩니다 — Claude가 LaTeX 소스를 요청합니다.

**내 논문을 채팅 언어로 번역하나요?** 아니요. 원고는 학회 언어로 유지되고, 대화만 당신이 고른 언어를
따릅니다.

**비 ACL 학회에도 되나요?** 됩니다. `references/venue-conventions.md`는 ACL 계열, Springer/LNCS/AJCAI,
ML 학회를 다룹니다. 그 외에는 Claude가 최신 CFP를 먼저 읽고 배운 것을 기록합니다.

## 9. 다른 AI 모델에서 사용하기(Codex, DeepSeek, Gemini …)

이 skill은 **모델과 무관한 Markdown 지침**일 뿐입니다 — Claude 전용은 `SKILL.md` 상단의 YAML
프런트매터(`---` 사이의 `name:`/`description:` 블록)뿐이며, 이는 자동 로딩용 메타데이터입니다. 그
아래는 모두 유능한 모델이라면 따를 수 있는 평범한 프롬프트입니다. 다른 곳에서 쓰려면:

1. **`SKILL.md` 본문을 복사** — 닫는 `---` *이후* 전부 — 다른 도구의 **시스템 프롬프트**에 붙여넣기
   (또는 첫 메시지로 붙여넣기). YAML 프런트매터는 버려도 됩니다. Claude의 skill 자동 발견에만 관계됩니다.
2. **참조 파일** `references/venue-conventions.md`를 컨텍스트로 첨부(붙여넣거나 도구의 파일/컨텍스트
   패널에 추가)해 모델이 학회 규칙을 갖게 합니다.
3. **논문도 같은 방식으로 제공** — 전체 개정은 `.tex` 소스, 리뷰 전용은 PDF — 그리고 대상 학회를 지명.

도구별 참고:

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, 로컬 모델:** `SKILL.md` 본문을 시스템/개발자 프롬프트로,
  venue-conventions 파일을 컨텍스트로 붙여넣고 위처럼 진행. 형식 변경 불필요 — 전부 산문 지침입니다.
- **Cursor / Windsurf / Continue 등 IDE 에이전트:** 도구가 읽는 프로젝트 규칙 파일(예: `.cursorrules`,
  `AGENTS.md`)에 `SKILL.md` 본문을 넣어 자동 로딩하고, `references/`를 저장소에 유지.
- **다를 수 있는 점:** 도구 호출과 파일 편집은 각 호스트 고유 메커니즘으로 일어나므로, 저장소 쓰기
  권한이 없는 모델도 **리뷰 전용**(읽고 코멘트)은 가능하지만 **전체 개정**에는 편집 가능한 호스트가
  필요합니다. 연구 진실성 규칙 — *결과 수치를 결코 지어내지 않음* — 은 프롬프트 자체에 적혀 있어, 지침을
  따르는 어떤 모델에도 이어집니다.
