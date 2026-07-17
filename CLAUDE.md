# CLAUDE.md · cardinal（上游 fork + 自研增强）

> 上游 = [cardisoft/cardinal](https://github.com/cardisoft/cardinal)（MIT，macOS 文件名极速搜索，Rust+Tauri）。
> 本 clone = 私有 fork，remote `mine` → `zengtianli/cardinal`（private）；`origin` = 上游只读。

## 本 fork 的增强（feat/search-history 分支）

**搜索历史**（上游只有内存态 ↑/↓ 翻历史，无持久化无 UI）：

- `cardinal/src/hooks/useRecentSearches.ts` — localStorage `cardinal.recentSearches`，最新在前、去重置顶、上限 50
- 记录时机：搜索框 Enter 提交 + 任何"打开结果"动作（双击/Cmd+O/右键菜单，经 `subscribeResultOpened`）
- UI：SearchBar 右侧时钟按钮 → 下拉面板（点条目复现查询 / Clear history / Esc·外点关闭）
- i18n：`search.history.*` 已铺 15 语言

## 构建 / 安装（已验证流程）

```bash
cd ~/Dev/cardinal/cardinal
npm install && npm test          # 前端测试（vitest）
npm run tauri build              # 需 rustup nightly（rust-toolchain.toml 钉版本，rustup 自动拉）
# 产物: cardinal/src-tauri/target/release/bundle/macos/Cardinal.app（ad-hoc 签名）
# 安装: 备份旧版进 ~/.Trash 后 ditto 到 /Applications/Cardinal.app
```

- `/Applications/Cardinal.app` 现 = 本 fork 自编译版（非上游 release）；升级上游 = `git fetch origin && git rebase origin/master feat/search-history` 后重编译
- toolchain 由 rustup 管（`~/.cargo/bin` 前置 PATH）；homebrew cargo 不认 rust-toolchain.toml
- 测试基线：272 上游 + 6 新增全绿；`npx tsc --noEmit` 干净

## 坑

- 浅 clone（--depth）推新 remote 会 "did not receive expected object" → 先 `git fetch --unshallow origin`
- 上游 AGENTS.md 写的 nightly 版本可能 stale，以 `rust-toolchain.toml` 为准
- app 的 UI 自动化测试：主搜索框左边有 folder-scope 输入框，synthetic click 容易点错焦点
