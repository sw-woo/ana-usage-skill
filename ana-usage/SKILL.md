---
name: ana-usage
description: ANA(Agent-Native Agent, anahub.io, tykimos/agent-native-agent)를 설치·실행하거나 브라우저(Playwright 등)로 조작할 때 사용. Codespace 생성, Claude 로그인 카드, PRD → Work 화면 빌드, ANA 채팅에 요청 보내기, 승인 카드, Chip, Knowledge/Requests/Evolve/Update/System 탭, 파일 첨부, anahub 활용 사례 등록, "*-8809.app.github.dev" 또는 "localhost:8809" 주소가 나오면 이 문서를 먼저 확인한다.
---

# ANA 사용법 (브라우저 자동 조작 포함)

ANA v0.27.11을 2026-11-25에 실제로 조작하고, 소스(`dashboard.html`, `dashboard-api.js`, `channel-core.js`, `permission-mode.js`, `work-ontology.js`, `skills/*/SKILL.md`)를 읽어 확인한 내용이다. 버전이 다르면 화면에서 다시 확인하고, 다른 점은 이 문서를 고친다.

## 1. 설치 흐름 (Codespace)

1. anahub.io → 실습 → 설치 → **Codespace 만들기** (`https://codespaces.new/tykimos/agent-native-agent?ref=main`).
2. GitHub 로그인. **사용자가 직접** 한다(비밀번호·2FA). `gh` 토큰에는 `codespace` 권한이 없을 수 있다.
3. VS Code 창이 열리면 **폴더 신뢰 및 계속**. 컨테이너 설치에 2~3분.
4. ANA 탭이 자동으로 열린다. 안 열리면 하단 **포트** 탭 → 8809 → 브라우저에서 열기. 주소는 `https://<codespace>-8809.app.github.dev/`.
5. 채팅의 Set up Claude Code 카드: 테마 → **Claude account with subscription** → Open Claude login → 코드 붙여넣기 → Complete login → **Yes, I trust this folder**. Claude/Google 로그인도 사용자가 직접 한다.
6. "안녕"을 보내 응답을 확인한다.

로컬 실행 시 주소는 `http://localhost:8809`.

## 2. 화면 구조

- **로고(Menu 버튼)** 를 누르면 탭 목록이 열린다: PRD · Work · Knowledge · Requests · Evolve · Update · System. 하위 탭은 위쪽 바에 나온다.
- **PRD**: Current / History. 버튼은 Edit, Sketch, Ask the agent. PRD가 없으면 **Talk to ANA** 로 시작한다. History의 Restore는 옛 내용을 새 판으로 저장한다(사라지는 것이 없다).
- **Work**: 에이전트가 만든 업무 화면. 비어 있으면 **Build the first screen from the PRD**.
- **Knowledge**: Ontology(종류·관계) / Graph / Documents(파일 추가, 25MB).
- **Requests / Evolve / Update**: Waiting · In progress · Done.
- **System**: 버전, Restart the server, Restart the agent.
- **채팅**: 오른쪽 패널. 닫혀 있으면 오른쪽 아래 주황 버튼(Open chat)으로 연다.
  - 세션 선택, 모델·Effort, 권한(Manual / Accept edits / Plan / Auto / Bypass), 파일 첨부(10MB), 음성, 사용량 링(5h·7d).
  - 에이전트 작업 중에는 보내기 버튼이 ■(중지)로 바뀐다.
- **Chip** 스위치: 켜고 화면 항목을 누르면 그 항목이 채팅 문맥 칩으로 붙는다. 새 Work 요소는 기본 대상이 아니므로 ⟳로 등록하거나 에이전트에게 칩 대상(CAP) 등록을 요청한다.
- **프로필 메뉴**: Edit My Info, Settings(테마·강조색), Log out.

## 3. 표준 작업 순서

1. PRD 탭 → Talk to ANA → 채팅에 "누가 쓰고, 무엇을 다루고, 무엇을 보고 싶은지"를 한 번에 적는다. "나머지는 기본값으로 정해서 PRD를 써 주세요"를 붙이면 질문 왕복이 줄어든다.
2. ANA가 PRD 저장 → 온톨로지 선언 → API 라우트 → Work 화면 → 테스트 → 서버 재시작까지 한다(3~7분, 큰 요청은 20분 이상).
3. **"New version available — tap to reload"** 알약이 뜨면 누른다(또는 페이지 재접속). 그 뒤 Work 탭을 연다.
4. Work 화면의 기능을 직접 써 보고, 고칠 점은 채팅으로 구체적으로 요청한다. 앱이 하는 일이 바뀌면 "PRD에도 반영"을 함께 요청한다.
5. 에이전트가 사람 데이터를 바꿀 때는 채팅에 **승인 카드**(Approve / Reject)가 뜬다. Change 줄을 읽고 승인한다. 여러 장이 차례로 올 수 있다. 승인 절차는 에이전트 지침(`CLAUDE.md`)의 규칙이지 서버가 강제하는 것은 아니다(승인 없이 적용하는 `/api/apply`도 있다).

## 4. 요청할 때 알아 둘 제약

- 메시지 한 건은 **8000바이트** 이하(한글 약 2,600자).
- 작업 중에 보낸 메시지는 대기열에 쌓여 순서대로 처리된다. 방향을 바꾸려면 "정정합니다…"라고 새로 보낸다(이전 PRD는 History에 남는다).
- 차트 라이브러리가 없다 → "SVG로 직접 그려 달라"고 요청한다.
- Work 항목 데이터는 `.ana/work-graph.json`에 평탄하게 저장된다. 값은 불리언·숫자·문자열(2만 자에서 잘림)·그 배열(100개에서 잘림)만 되고, 객체를 넣으면 빈 문자열로 저장된다.
- 메일 발송, 푸시 알림, 헤드리스 브라우저, 시세 API가 없다. 외부 데이터는 에이전트가 웹 검색으로 가져오므로 출처마다 값이 다를 수 있다 → 출처 수와 확인 여부 표시를 요청하고, 수치를 그대로 믿지 않는다.
- 자동 실행(예약)은 에이전트 세션 안의 예약이라 세션이 끝나거나 Codespace가 멈추면 돌지 않는다.
- ANA 에이전트는 자기 화면을 볼 수 없다. 화면 검증은 사람이나 브라우저 자동화로 직접 한다.
- 같은 파일을 고치는 요청이 여럿이면 "겹치지 않게 순서대로"라고 적는다.

## 5. Requests · Evolve · Update

### Requests

- **Ask the agent**: 에이전트가 사람에게 받아야 할 것(승인·결정·접근·행동·정보)을 카드로 등록한다.
- 카드 버튼: Send(답 보내기) / Resolved·I did it / Discuss / Not now.
- 이름·관심사·운영 방식처럼 사용자만 정할 수 있는 항목은 대신 답하지 않는다.

### Evolve

- **Ask the agent** → 2~7분 뒤 Waiting에 tidy / improve / reimagine 카드가 올라온다.
- 버튼 위치: Waiting 카드는 Discuss / Do it / Ignore. In progress 카드는 Discuss / Mark done / Failed / Cancel. Done 칸에서 끝낸 카드는 Reopen, 무시·취소한 카드는 Restore, 실패한 카드는 Try again. 셋 다 카드를 Waiting으로 되돌린다.
- **Do it, Ignore, Cancel은 확인 창 없이 바로 실행된다.** 카드를 고를 때 `#번호`와 제목을 함께 확인한다.
- **Discuss**는 제안을 칩으로 붙이고 채팅을 연다. 큰 제안은 먼저 조건을 말하면 에이전트가 허점을 짚어 준다. "구현은 Do it을 누른 뒤에"라고 적어 두면 논의만 한다.
- **Cancel**은 Ignore와 같은 동작이다(카드가 "Ignored"로 표시되고 Restore가 붙는다). 에이전트에게는 아무 알림도 가지 않으므로, 이미 보낸 Do it 지시는 그대로 구현된다. 정말 막으려면 채팅으로 따로 알린다. 에이전트가 일을 끝내 완료 처리하면 카드는 Done(Reopen)으로 바뀐다.
- 제안의 질은 근거 자료에 달려 있다. Knowledge › Documents에 설계 문서(알려진 문제 목록 포함)를 올리고 Knowledge의 Ask the agent를 먼저 누르면, 그 문서를 근거로 제안한다.
- 외부 설정(GitHub Actions 등)이나 사용자 정보가 필요한 제안은 사용자에게 먼저 확인한다.

### Update

- **Check for updates**는 저장소 커밋과 가진 기능 수(예: "20/20 features here")를 보여 준다.
- 새로 가져올 것이 있으면 카드에 Bring it in / Skip이 나온다. 없으면 카드가 없다.
- Bring it in은 앱 코드를 바꾼다. 직접 만든 Work 화면과 충돌할 수 있으니 사용자 확인 후 누른다.

## 6. 파일 넣기와 꺼내기

- 채팅의 `#attachBtn` → 파일 선택 → 메시지와 함께 전송. 단일 HTML(게임 등)도 이렇게 넣어 "Work 하위 탭 + 전용 라우트 + iframe"으로 만들 수 있다. 같은 출처라 `iframe.contentWindow`로 접근해 검증할 수 있다.
- Knowledge › Documents는 `#docAdd`.
- 에이전트가 고친 파일을 로컬로 가져오려면, 그 주소에서 `fetch('<경로>')`의 텍스트를 `browser_evaluate`의 `filename`으로 저장한다(JSON 문자열로 저장되므로 `json.loads`로 푼다).
- anahub 활용 사례 등록: anahub.io/usecases → "＋ 내 ANA 공유하기"(로그인 필요) → `#fTitle`, `#fSum`, `#fBody`, 해시태그 `#fTagIn`(Enter로 추가, 최대 8개), 이미지는 `#drop` 클릭 후 파일 선택(최대 6장), 주소 `#fUrl`, 제출 `#fSubmit`. **공개 게시물이므로 캡처에 이름·이메일이 없는지 확인한다.** Codespace 주소는 비공개라 사이트 주소로 쓰지 않는다.

## 7. Playwright로 조작할 때 요령

- 페이지 스냅샷은 크다. `filename`으로 저장한 뒤 `grep`으로 필요한 `ref`만 찾는다. 재렌더링되면 `ref`가 바뀌므로 클릭 직전에 다시 찾는다.
- 채팅 입력: `role=textbox[name="Message input"]`에 입력하고 `submit: true`.
- 탭 이동: `[aria-label="Menu"]` 클릭 → `[role=tab]` 중 이름이 일치하는 것 클릭. Requests/Evolve/Update는 배지 숫자가 붙으므로 `startsWith`로 찾는다. 하위 탭은 `#topSub [role=tab]`.
- 에이전트 작업 여부는 `[aria-label="Stop the current task"]` 버튼이 보이는지로 판단한다. 완료 대기 루프에서는 Approve 버튼이 생겼는지도 함께 본다.
- `browser_evaluate`는 120초를 넘기면 백그라운드로 넘어간다. 한 번에 100초 안쪽으로 걸거나, 긴 대기는 일부러 백그라운드로 보내고 알림을 받는다. `browser_wait_for`를 여러 개 병렬로 호출하면 동시에 끝나 대기가 늘지 않는다.
- 확인 창은 앱 내부 모달(`#scrim`)이다. 모달이 떠 있으면 뒤쪽 버튼 클릭이 막힌다 → 스크린샷으로 모달 내용을 확인하고 모달 안 버튼을 누른다.
- 채팅 패널이 Work 화면을 가리면 `#chatCloseBtn`으로 닫는다.
- 목록 항목은 `<button>`이 아니라 `[role=button]`일 수 있다. 못 찾으면 선택자를 넓힌다.
- 입력값을 스크립트로 넣을 때는 네이티브 value setter 뒤에 `input`·`change` 이벤트를 보낸다.
- 상태가 바뀐 뒤에는 화면 텍스트로 결과를 확인한다(예: 필터 개수, "보냄 · 시각").

## 8. Codespace가 멈췄을 때

- 증상: ANA 주소가 401 / `ERR_HTTP_RESPONSE_CODE_FAILURE`, VS Code 탭에 "Stopping codespace…" 또는 "Codespace is stopped".
- 원인: VS Code 쪽에 30분간 활동이 없으면 멈춘다. ANA 화면(포트)만 쓰는 것은 활동으로 치지 않는다. VS Code에 단축키만 보내는 것도 인정되지 않았다.
- 복구: `https://<codespace>.github.dev/`를 다시 열면 1분 안에 재시작되고 ANA 탭이 새로 열린다. 데이터와 대기열은 남는다. 에이전트가 가만히 있으면 "이어서 해 주세요"를 보낸다.
- 예방: 만들기 전에 GitHub Settings › Codespaces › Default idle timeout을 늘린다(최대 240분, 새로 만드는 Codespace부터 적용). 이미 켠 뒤라면 VS Code 터미널에 `for i in $(seq 1 22); do date; sleep 240; done`처럼 **횟수가 정해진** 출력 루프를 건다. 무한 루프는 사용량이 계속 나가므로 쓰지 않는다.
- github.com/codespaces에서 어떤 Codespace가 Active인지 확인한다. 같은 브라우저를 사용자가 함께 쓰고 있을 수 있으니, 내가 열지 않은 탭·Codespace는 건드리지 않는다.

## 9. 지켜야 할 것

- 로그인(비밀번호·2FA·OAuth 계정 선택)은 사용자가 직접 한다. 막히면 그 화면에서 멈추고 알린다.
- 실제 외부 발송(메일 등)과 공개 게시는 사용자에게 방식과 대상을 확인한 뒤에만 한다. 모르는 이메일 주소는 추측하지 않는다.
- 승인 카드는 Change 내용을 읽고, 사용자가 준 정보나 맡긴 일과 일치할 때만 승인한다.
- 삭제, Restore(PRD), Restart, Bring it in처럼 되돌리기 어려운 버튼은 사용자 요청이 있을 때만 누른다.
- 서버를 다시 띄울 때는 System › Restart the server 또는 `run.sh restart`. `killall node`는 쓰지 않는다.
- `channel-core.js`는 고치지 않는다(공유 런타임).

## 10. 문제 해결

| 증상 | 조치 |
|---|---|
| 고친 화면이 안 보임 | "New version available" 알약 클릭 또는 재접속. API를 바꿨으면 Restart the server |
| "Agent input line is not ready" | 터미널에 로그인·신뢰 메뉴가 남아 있음. 채팅의 Terminal view & keys 카드로 처리 |
| 말풍선이 Unconfirmed / Send failed | Resend |
| 승인했는데 "unknown list" | `work-ontology.js`에 종류 선언 후 Try again |
| Server connection lost / 401 | Codespace가 멈춘 것. 8절 참고 |
| Recovery mode | 컨테이너 설치 실패. 생성 로그 확인 |
| anahub 로그인에서 400 | 오래 열어 둔 로그인 화면일 수 있음. 로그인 페이지를 새로 열어 다시 시도 |
