# TNT Korea

## Claude Code 환경 셋업 / 동기화

새 PC 든 기존 PC 든 **이 한 줄 하나로 항상 최신 상태**가 된다.

### 1. 사전 1회 — `gh auth login`

PowerShell 열고:

```powershell
gh auth login
```

- `GitHub.com` → `HTTPS` → `Login with a web browser` 선택
- 8자리 코드를 브라우저에 입력하면 인증 완료

> `gh` 가 안 깔려있으면 먼저 `winget install --id GitHub.cli` (PowerShell 새 창 필요)

### 2. 동기화 한 줄 (셋업 + 업데이트 통합)

`.claude` 폴더가 **없든 / 있든 / 망가져있든 / 이미 git repo 든** 한 번에 최신으로 맞춰준다. 매번 그대로 다시 실행해도 안전 — 데이터 (대화 히스토리, `projects/`, `.credentials.json` 등) 는 보존되고 설정/스킬/커맨드만 최신으로 갱신된다.

```powershell
$t="$env:USERPROFILE\.claude"; $r="tntkorea-glitch/claude-config"; if (Test-Path "$t\.git") { git -C $t fetch origin; $b=(git -C $t symbolic-ref --short refs/remotes/origin/HEAD) -replace '^origin/',''; git -C $t reset --hard "origin/$b" } elseif (Test-Path $t) { $tmp=Join-Path $env:TEMP ".claude-sync-$(Get-Random)"; gh repo clone $r $tmp; Move-Item "$tmp\.git" "$t\.git"; Remove-Item $tmp -Recurse -Force -ErrorAction SilentlyContinue; git -C $t reset --hard HEAD } else { gh repo clone $r $t }; if (Test-Path "$t\setup-windows.ps1") { powershell -ExecutionPolicy Bypass -File "$t\setup-windows.ps1" }
```

완료되면 **PowerShell 새 창 열고 `cc` 입력** → Claude Code 실행.

### 3. 케이스별 동작 (한 줄이 자동 분기)

| 상황 | 동작 |
|---|---|
| `.claude` 없음 | 신규 clone |
| `.claude` 있는데 git repo 아님 (Claude Code 가 자동 생성한 껍데기) | 임시 clone → `.git` 만 옮겨와서 git repo 화 → 최신 덮어쓰기 |
| `.claude` 가 이미 git repo | `fetch` + `reset --hard origin/main` 으로 강제 정렬 |

### 4. 트러블슈팅

**`destination path '...\.claude' already exists` 에러**
→ 옛날 단순 `gh repo clone` 부트스트랩의 한계. **위 2번 한 줄로 다시 실행**하면 끝. (이 한 줄은 모든 케이스를 처리한다)

**`gh auth status` 가 not logged in**
→ `gh auth login` 다시 실행

**`cc` 명령이 안 먹는다**
→ PowerShell 새 창을 다시 열어라 (`$PROFILE` 갱신 반영). 그래도 안 되면 setup 스크립트 직접 실행:
```powershell
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.claude\setup-windows.ps1"
```

**스킬(`/pic`, `/cc`, `/md` 등) 이 인식 안 된다**
→ `.claude\skills\` 가 비어있다는 뜻. 위 2번 한 줄을 실행하면 채워진다.

### 사전 요구사항

- [Git](https://git-scm.com/download/win)
- [GitHub CLI](https://cli.github.com) — `gh auth login` 인증 필수
- Claude Code — `npm i -g @anthropic-ai/claude-code`
- [Vercel CLI](https://vercel.com/cli) (선택) — `npm i -g vercel`

### 동작 방식

- `~/.claude` = `tntkorea-glitch/claude-config` 의 working tree (git repo)
- `cc` 실행할 때마다 자동 `git pull` → 다른 PC 에서 바꾼 설정이 자동 반영
- 프로젝트 폴더 열면 SessionStart 훅이:
  1. 프로젝트 repo 자동 clone/pull
  2. `.claude-memory/` 복원
  3. `vercel link --yes` 자동 실행 (처음 여는 프로젝트에서)
  4. `.env.local` 자동 pull (Vercel 등록된 값)
