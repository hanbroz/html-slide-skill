---
name: html-slide-skill
description: Use when asked to turn a decision-making report, technical manual, or long-form document into an HTML slide deck (rather than one long HTML page) — triggers on "슬라이드로 만들어줘", "보고서를 슬라이드로", "의사결정용 슬라이드", "HTML 슬라이드", "decision deck", "slide deck for review", or when a report/manual is dense enough that a single-page HTML would be hard to scan.
---

# HTML Slide Skill

의사결정 리포트·매뉴얼을 한 개의 긴 HTML 대신, 한 화면에 하나씩 넘겨보는 **HTML 슬라이드 덱**으로 만든다. 목적은 항상 하나: 독자가 문서를 완벽하게 이해하는 것. 길이 제한은 없다 — 이해에 필요하면 슬라이드를 더 쪼갠다.

## When to use

| 증상 | 판단 |
|---|---|
| 보고서/매뉴얼 내용이 많아 한 페이지 HTML로는 스크롤이 길고 가독성이 떨어짐 | 이 스킬 사용 |
| 발표/의사결정 회의에서 단계적으로 보여줘야 함 | 이 스킬 사용 |
| 표 1~2개, 짧은 안내문 수준의 가벼운 내용 | 과함 — 그냥 짧은 HTML 문서로 충분 |
| 발표자 노트, 인쇄용 PDF 내보내기 등 프레젠테이션 도구 기능이 필요 | `md-slides` 스킬(별도 markdown-html 파이프라인) 검토 |

## Non-negotiable rules

1. **두괄식.** 1번 슬라이드는 결론/권고안을 한 문단으로 압축. 본문 슬라이드보다 먼저.
2. **챕터 구분.** 내용이 여러 주제로 나뉘면 챕터마다 표지 슬라이드를 넣는다. 챕터 표지는 본문 슬라이드와 **다른 배경**(예: 짙은 색 배경)을 쓰고, 그 챕터에서 다룰 내용을 요약한다.
3. **라이트 테마 고정.** 다크모드 토글 없음. 챕터 표지의 어두운 배경은 예외적 디자인 요소일 뿐, 문서 전체 테마 전환 기능은 만들지 않는다.
4. **이모지 금지.** 비즈니스 발표 톤을 유지한다.
5. **다이어그램은 Mermaid + 전체화면 버튼 필수.** 모든 Mermaid 다이어그램 카드에는 "전체화면" 버튼을 붙이고, 클릭 시 렌더링된 SVG를 확대된 모달로 보여준다.
6. **코드/CLI는 syntax highlight.** highlight.js 등으로 하이라이트 처리한다. 맨 텍스트로 붙여넣지 않는다.
7. **표/차트/이미지를 적극 사용.** 텍스트로만 설명 가능한 내용도 표나 다이어그램으로 바꿀 수 있으면 바꾼다.
8. **마지막 슬라이드 2장: 출처 → 참고자료.** "출처"는 본문에서 실제로 인용한 근거(웹 자료는 URL 필수). "참고자료"는 본문에 직접 인용하진 않았지만 이해에 도움되는 배경 자료/용어.
9. **UI는 비즈니스 톤의 taste skill 원칙을 적용.** 로컬에 `design-taste-frontend` 스킬이 설치돼 있으면 그것을 로드해 팔레트·타이포·여백 원칙을 따른다(이것이 tasteskill.dev가 요구하는 "깔끔한 비즈니스 UI"의 로컬 대응물). 설치돼 있지 않으면 이 스킬의 기본 팔레트(`assets/template.html`의 CSS 토큰)를 그대로 쓴다.
10. **완성 후 3-에이전트 이상 리뷰, 그중 최소 1개는 Red Team.** 아래 "완성 후 검증" 참고.
11. **폰트.** Noto를 기본으로 사용하고 Taste Skill이 다른 폰트를 제안하는 경우 변경

## Building the deck

`assets/template.html`을 복사해서 시작한다. 단일 파일 HTML(빌드 도구 없음)이며 이미 구현된 것:

- 슬라이드 전환: 방향키(←/→), PageUp/Down, Home/End, 하단 이전/다음 버튼, 진행률 바, 카운터(`N / total`)
- 표지 슬라이드(`.slide--cover`) — 두괄식 결론 박스 포함
- 챕터 표지 슬라이드(`.slide--chapter`) — 어두운 배경 + 챕터 요약
- 표, Mermaid 다이어그램(+전체화면 모달), 코드 하이라이트, 출처/참고자료 섹션 각각 예시 1개씩

섹션(`<section class="slide">`)을 복사·추가해서 필요한 만큼 슬라이드를 늘린다. `nav`, `progress`, 모달, 스크립트는 건드릴 필요 없음 — `document.querySelectorAll(".slide")` 기준으로 자동 동작한다.

**주의 (실제로 겪은 버그):** 슬라이드를 `display:none`으로 숨기면 안 보이는 슬라이드의 Mermaid가 크기를 측정하지 못해 `translate(undefined, NaN)` 오류와 함께 다이어그램이 빈 카드로 남는다. 템플릿은 `opacity:0; pointer-events:none`으로 숨기는 방식을 이미 쓰고 있으니 이 구조를 유지한다.

전체화면 모달은 렌더링된 `<svg>`를 복제해서 키우는데, Mermaid가 svg에 박아두는 `style="max-width:...px"`부터 지워야 실제로 커진다 (`clone.style.maxWidth = "none"` 다음에 `width` 설정). 템플릿에 이미 반영돼 있다.

외부 리소스(Mermaid, highlight.js)는 CDN에서 **버전 고정 + SRI 해시**로 로드한다. 새 버전을 쓰려면 해시를 다시 계산해서 넣는다(하드코딩된 예전 해시를 그대로 복사하면 무결성 검증에 실패해 스크립트가 로드되지 않는다).

## 완성 후 검증

배포/공유 전에 서브에이전트 3개 이상을 생성해 교차 검증한다. 최소 1개는 Red Team(문서를 깨뜨리려는 관점: 숫자 불일치, 과장된 주장, 근거 없는 결론, 인용 누락을 공격적으로 찾는다)으로 지정한다. 나머지는 형식(챕터 구조·두괄식·출처 형식 준수), 내용/논거(근거와 결론의 논리적 연결), 표현(어려운 용어·이해하기 힘든 문장) 중 겹치지 않게 나눠 맡긴다. 지적된 사항은 반영 후 재검토한다.

가능하면 Playwright 등으로 실제 브라우저에 열어 슬라이드 전환·Mermaid 렌더링·전체화면 버튼·코드 하이라이트가 실제로 동작하는지 최소 1회 확인한다.
