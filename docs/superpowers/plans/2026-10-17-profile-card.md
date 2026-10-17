# 개인 명함 페이지 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox syntax for tracking.

**Goal:** 기존 글 주소를 보존하면서 루트 화면을 개발자 동료에게 공유할 미니멀 명함으로 교체한다.

**Architecture:** 기존 정적 사이트의 index.html과 명함 전용 CSS만 변경한다. 링크는 브라우저 기본 동작을 사용하며 과거 Hexo 공용 스타일과 콘텐츠는 유지한다.

**Tech Stack:** HTML5, CSS, 로컬 검증용 Python HTTP 서버, 사용 가능한 브라우저 도구.

**Spec:** `docs/superpowers/specs/2026-10-17-profile-card-design.md`

## Global Constraints

- 아이보리 #F7F5F0, 차콜 #252824, 딥그린 #285447, 최대 콘텐츠 폭 560px, 내부 왼쪽 정렬.
- 이름 SEONG SEOB KIM, 핸들 sskim91, 소개 “서비스를 만들고, 배포하고, 운영하는 개발자입니다.”
- 분야 Backend · Infrastructure · AI Applications, 위치 Seoul, Korea.
- 링크 https://github.com/sskim91, https://amagrammer91.tistory.com/, mailto:sskim91@seongseob.dev, /archives/.
- 시스템 글꼴, 최소 44px 링크 터치 영역, 키보드 포커스, 모바일 및 확대 대응.
- JavaScript, 프레임워크, 빌드 의존성, 필수 애니메이션을 추가하지 않는다.
- 기존 글과 공용 자산은 변경하지 않는다. 원격 push와 공개 배포는 별도 확인한다.

## Review Focus

- 320px 화면에서 긴 영문 이름과 분야가 가로 넘침 없이 줄바꿈되는지 확인한다.
- 데스크톱 200% 확대에서 링크 및 하단 기록 링크에 접근 가능한지 확인한다.
- 마우스 없이 Tab/Enter로 모든 링크를 구별하고 사용할 수 있는지 확인한다.
- JavaScript가 꺼진 브라우저에서도 소개와 링크가 그대로 작동하는지 확인한다.
- 기존 archives 목록에서 한글 URL의 글과 관련 스타일·이미지가 정상 로드되는지 확인한다.

## 파일 구성

- Modify: `index.html` — 소개, 탐색 링크, 문서 메타 정보.
- Create: `css/profile.css` — 명함 전용 반응형 스타일과 포커스 상태.
- 과거 콘텐츠 파일은 검증만 수행한다. 이 정적 UI 변경에는 구현을 반복하는 테스트 파일을 추가하지 않고 브라우저 및 HTTP 검증을 수행한다.

### Task 1: 명함 구현 및 동작 확인

**Interfaces:** 입력은 설계의 확정 문구와 네 링크다. 결과는 `/`에서 제공되는 HTML과 `/css/profile.css`이며 기존 `/archives/`와 글 URL을 그대로 이용한다.

- [x] **Step 1: 작업 격리와 기준 상태 확보.** using-git-worktrees 스킬에 따라 현재 설계 커밋을 포함하는 격리 작업 공간을 만든다. 기준 SHA와 git status를 기록하고 기존 archives 및 대표 글 URL을 선정한다. 이 계획 문서도 실행 작업 공간에 포함한다.
- [x] **Step 2: index.html 교체.** lang=ko, viewport(확대 제한 없음), main, h1, 소개, nav, footer로 구성한다. 링크 텍스트는 GitHub, Tistory, Email, 이전 기록으로 하고 새 창을 강제하지 않는다. 제목은 “SEONG SEOB KIM | Developer”, description은 확정 소개, canonical 및 og:url은 https://sskim91.github.io/, og:title과 og:description은 제목·소개와 일치시킨다. 명함은 css/profile.css만 로드하고 기존 favicon은 유지한다.
- [x] **Step 3: css/profile.css 작성.** 설계 색상과 폭을 적용하고 clamp 기반 이름 크기, 자연 줄바꿈, 유연한 링크 배치, 최소 터치 영역, 명확한 focus-visible을 구현한다. 본문은 최소 높이로 중앙 배치하며 콘텐츠가 커지면 정상 스크롤한다.
- [x] **Step 4: HTTP 및 링크 검증.** `python3 -m http.server 8000 --bind 127.0.0.1`로 제공한다. `/`, `/css/profile.css`, `/archives/`, 선정한 기존 글과 그 로컬 자산이 200인지 확인한다. 브라우저 DOM에서 네 href 및 메타 값을 위 값과 대조한다. 이메일은 mailto 목적지만 확인하고 메일 발송은 하지 않는다.
- [x] **Step 5: 시각·접근성 확인.** 375×812와 1440×900 스크린샷을 확인한다. 추가로 320px 폭, 200% 확대, 키보드 Tab/Enter, JavaScript 비활성 조건에서 Review Focus 항목을 점검한다. 가로 넘침, 잘림, 링크 겹침이 없고 본문 대비가 WCAG AA 4.5:1 이상인지 확인한다. 사용 가능한 도구가 일부 검증을 지원하지 않으면 미검증 범위를 명시한다.
- [x] **Step 6: 보존 및 변경 검사.** `git diff --check`가 통과하고 `git diff --name-only <기준SHA>`에 과거 글·공용 자산 변경이 없는지 확인한다. 미추적 파일까지 포함해 허용 파일과 설계·계획 문서만 변경되었는지 확인한다.
- [x] **Step 7: 리뷰와 로컬 완료 보고.** 실행 방식에 맞춰 리뷰하고 발견된 문제만 수정·재검증한다. 구현 결과, 로컬 미리보기, 검증 결과를 제시한다. 로컬 커밋 시 명시적으로 index.html, css/profile.css 및 합의한 문서만 stage하고 메시지는 `feat(profile): 미니멀 개발자 명함 페이지 추가`로 한다. 원격 배포는 수행하지 않는다.


## 실행 결과

- 구현 커밋: 72cd9a3, 브랜치 feat/profile-card.
- 변경: index.html, css/profile.css. 기존 글과 공용 자산 diff 없음.
- 320/375/1440px 가로 넘침 없음, 링크 높이 모두 44px. 375×812 / 1440×900 스크린샷 육안 확인.
- 키보드 네 링크 포커스 및 Enter로 archive 이동 확인. JavaScript 비활성에서도 소개·링크·archive 이동 확인.
- 메인·CSS·archive·대표 한글 글 및 관련 로컬 자산 19개 HTTP 200 확인.
- 텍스트 대비 13.69 / 5.38 / 7.87, 메타 정보와 링크 주소 일치, git diff --check 통과.
- 독립 리뷰: Critical / Important / Minor 모두 없음. 리뷰어는 코드·모바일 이미지를 확인했으며 상호작용·HTTP 결과는 구현자의 기록을 참고함.
- 한계: 확대는 CSS zoom=2로 확인했으며 실제 브라우저 UI 확대와 실기기, 메일 발송 및 공개 배포는 미검증.
- 실행 판단: 네이티브 worktree 도구는 dotfiles에 연결되어 대상 저장소 인자를 받지 않으므로 수동 Git worktree를 사용함. 영향은 작업 공간 등록 방식에 한정됨.
- 검증 판단: 상위 지침과 승인된 계획에 따라 정적 UI를 반복하는 테스트 파일 대신 브라우저·HTTP 검증을 사용함. 지속적인 자동 회귀 테스트는 추가되지 않음.
- 리뷰 유보 범위 판단: 상호작용과 HTTP는 구현자가 직접 검증했으므로 중복 리뷰 실행을 요구하지 않음. 실제 브라우저 확대·실기기 호환성은 추가 확인 영역으로 남김.
