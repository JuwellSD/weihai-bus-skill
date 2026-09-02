# weihai-bus — Claude Code Skill

威海实时公交查询 CLI 的 **Claude Code Skill**（Vercel 风格）。让 AI agent 能直接通过
`weihai` 命令查询威海（及多城市）实时公交，而不必从零读文档或手写 API 调用。

> 本仓库仅含 skill 文档（`SKILL.md` + `references/`）。CLI 与 SDK 本体在
> [ShineDer/weihai-bus-sdk](https://github.com/ShineDer/weihai-bus-sdk)。

## Skill 内容

- **`SKILL.md`** — frontmatter（含 `metadata.requires.bins: ["weihai"]`）+ 使用原则 /
  适用场景 / 反唤起信号 / 命令索引。
- **`references/`** — 每条/组子命令一份文档，采用 `input` / `action` / `output` /
  `side_effect` / 失败处理的 Vercel 风格结构化描述：
  - `weihai-routes.md` — `weihai routes`
  - `weihai-vehicles.md` — `weihai vehicles`
  - `weihai-stops.md` — `weihai stops` + `weihai nearby`
  - `weihai-next.md` — `weihai next-at-stop` + `weihai next`

## 安装到 Claude Code

把整个仓库内容放到 Claude Code 的 skill 目录（任一即可）：

- **用户级（全局，推荐）**：`~/.claude/skills/weihai-bus/`
- **项目级**：`<project>/.claude/skills/weihai-bus/`

直接拷贝即可：

```bash
git clone git@github.com:ShineDer/weihai-bus-skill.git
mkdir -p ~/.claude/skills/weihai-bus
cp -r weihai-bus-skill/* ~/.claude/skills/weihai-bus/
```

重开/新开一个 Claude Code 会话后生效，可在 `/skills` 列表看到 `weihai-bus`。

## 依赖

skill 依赖 `weihai` CLI 命令可用（否则 agent 无法真正执行查询）：

```bash
uv tool install weihai-bus        # 由 SDK 仓库提供 weihai 命令
```

## 修改与维护

- CLI 子命令新增/改动时，同步更新 `references/*.md` 的 `input` 参数表、示例与 `output`。
- 改动 `weihai` 命令集时，保持 `metadata.requires.bins` 与 CLI 能力一致。
- 本地实际装载位置在 `~/.claude/skills/weihai-bus/`，修改后记得同步（或用 symlink 指向此仓库以启用 `git pull` 更新）。