# TNT Korea

## 새 Windows PC 에서 Claude Code 셋업

### ⚠️ 먼저 `gh auth login` 필수 (이거 안 하면 clone 실패함)

PowerShell 열고 **1번 실행 (최초 1회)**:

```powershell
gh auth login
```

- `GitHub.com` → `HTTPS` → `Login with a web browser` 선택
- 8자리 코드를 브라우저에서 입력하면 인증 완료

### 인증 후, 아래 한 줄 복붙

```powershell
gh repo clone tntkorea-glitch/claude-config $env:USERPROFILE\.claude; if (Test-Path "$env:USERPROFILE\.claude\setup-windows.ps1") { powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.claude\setup-windows.ps1" } else { Write-Host "[ERROR] Clone failed. Run 'gh auth login' first, then retry." -ForegroundColor Red }
```

완료되면 **PowerShell 새 창을 열고 `cc` 입력** → Claude Code 실행.

### 사전 요구사항

- [Git](https://git-scm.com/download/win)
- [GitHub CLI](https://cli.github.com) — 위에서 `gh auth login` 인증 필요
- Claude Code — `npm i -g @anthropic-ai/claude-code`
- [Vercel CLI](https://vercel.com/cli) (선택) — `npm i -g vercel`

### 동작 방식

- 첫 실행 시 `~/.claude` 가 `tntkorea-glitch/claude-config` 에서 clone 됨
- 이후 `cc` 실행할 때마다 자동으로 `git pull` → 다른 PC에서 변경한 설정이 자동 반영
- 프로젝트 폴더 열면 SessionStart 훅이:
  1. 프로젝트 repo 자동 clone/pull
  2. `.claude-memory/` 복원
  3. `vercel link --yes` 자동 실행 (처음 여는 프로젝트에서)
  4. `.env.local` 자동 pull (Vercel 등록된 값)
