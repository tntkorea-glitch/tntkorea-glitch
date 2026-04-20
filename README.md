# TNT Korea

## 새 Windows PC 에서 Claude Code 셋업

PowerShell 열고 아래 한 줄 복사/붙여넣기:

```powershell
gh repo clone tntkorea-glitch/claude-config $env:USERPROFILE\.claude; powershell -ExecutionPolicy Bypass -File $env:USERPROFILE\.claude\setup-windows.ps1
```

완료되면 **PowerShell 새 창을 열고 `cc` 입력** → Claude Code 실행.

### 사전 요구사항

- [Git](https://git-scm.com/download/win)
- [GitHub CLI](https://cli.github.com) — `gh auth login` 필수 (private repo 접근용)
- Claude Code — `npm i -g @anthropic-ai/claude-code`

### 동작 방식

- 첫 실행 시 `~/.claude` 가 `tntkorea-glitch/claude-config` 에서 clone 됨
- 이후 `cc` 실행할 때마다 자동으로 `git pull` → 다른 PC에서 변경한 설정이 자동 반영
- 프로젝트 폴더 열면 SessionStart 훅이 `.env.local` 자동 pull (Vercel)
