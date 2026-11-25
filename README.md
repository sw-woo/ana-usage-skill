# ana-usage — Claude Code용 ANA 사용법 스킬

[ANA(Agent-Native Agent)](https://www.anahub.io/)를 Claude Code로 다룰 때 자동으로 참조되는 스킬입니다. Claude Code가 ANA를 설치하고, 화면을 조작하고, ANA 에이전트에게 일을 맡길 때 알아야 할 것을 한 문서에 담았습니다.

- 설치 흐름(Codespace)과 화면 구조
- 요청 요령과 제약(메시지 8000바이트, 평탄한 데이터, 없는 기능)
- Requests · Evolve · Update 버튼별 실제 동작
- 파일 넣기·꺼내기, anahub 활용 사례 등록
- 브라우저 자동화(Playwright) 요령
- Codespace 유휴 정지 대응
- 지켜야 할 것(로그인·발송·공개 게시는 사람 확인 후)

ANA v0.27.11(커밋 `7c92b53`)을 2026-11-25에 실제로 조작하고 소스를 읽어 확인한 내용입니다. 버전이 다르면 화면이 다를 수 있습니다.

## 설치 (한 줄)

내 모든 프로젝트에서 쓰기:

```bash
git clone https://github.com/sw-woo/ana-usage-skill /tmp/ana-usage-skill && mkdir -p ~/.claude/skills && cp -R /tmp/ana-usage-skill/ana-usage ~/.claude/skills/
```

이 프로젝트에서만 쓰기(팀과 저장소로 공유):

```bash
git clone https://github.com/sw-woo/ana-usage-skill /tmp/ana-usage-skill && mkdir -p .claude/skills && cp -R /tmp/ana-usage-skill/ana-usage .claude/skills/
```

Claude Code를 다시 시작하면 스킬 목록에 `ana-usage`가 보입니다.

ANA를 Codespace에서 쓰고 있다면, Codespace 안의 Claude Code(ANA 에이전트)가 아니라 **내 컴퓨터의 Claude Code**에 설치하는 스킬입니다. 내 컴퓨터의 Claude Code가 브라우저로 ANA를 다루게 하는 용도입니다.

## 쓰는 법

따로 부를 필요 없습니다. 아래처럼 ANA 관련 요청을 하면 Claude가 이 문서를 먼저 읽습니다.

- "anahub 보고 ANA Codespace 설치 진행해 줘"
- "이 ANA 주소(`…-8809.app.github.dev`)에서 Evolve 제안 받아서 맡겨 줘"
- "ANA에 이 파일 넣고 Work 탭으로 만들어 줘"

직접 부르려면 `/ana-usage`를 입력합니다.

브라우저를 직접 조작하게 하려면 Playwright MCP 같은 브라우저 도구가 Claude Code에 연결돼 있어야 합니다. 없어도 사용법 안내용으로는 쓸 수 있습니다.

## 알아 둘 것

- GitHub·Claude·anahub 로그인은 사람이 직접 해야 합니다. 스킬도 그렇게 하도록 적혀 있습니다.
- 실제 메일 발송이나 공개 게시는 사용자 확인 후에만 하도록 적혀 있습니다.
- Evolve의 Failed·Restore·Try again, Update의 Bring it in / Skip은 코드로만 확인했고 직접 눌러 보지는 못했습니다.
- ANA가 업데이트되면 버튼 이름이나 선택자가 달라질 수 있습니다. 달라진 점을 발견하면 `ana-usage/SKILL.md`를 고쳐 쓰세요. 고친 내용을 이 저장소에 보내 주시면 반영하겠습니다.

## 구성

```
ana-usage-skill/
├── README.md          이 파일
└── ana-usage/
    └── SKILL.md       스킬 본문
```

ANA 자체는 [tykimos/agent-native-agent](https://github.com/tykimos/agent-native-agent)에 있습니다. 이 저장소는 ANA의 공식 문서가 아니라 사용자가 정리한 사용법입니다.
