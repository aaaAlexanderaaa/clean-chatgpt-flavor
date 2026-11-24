# Clean ChatGPT Flavor

> **English version**: [README.md](README.md)

一个完全自包含的 Agent Skill，用来清理和对抗 ChatGPT 式过度防御生成（"ChatGPT flavor"）——覆盖文档、文案、提示词、代码、测试、JSON Schema、LLM Payload 与数据处理流程。

## 解决什么问题

ChatGPT 风格的写作与编程容易滑向“法证审计员 / 防追责律师”姿态——把内部可信步骤和普通读者都当成敌对审查方：

1. **焦虑文案与免责叠甲**：在文档、UI 卡片、图表脚注和 CLI 输出里堆砌 `"不能证明"`、`"并不等于"`、`"不是…而是…"`、`"is not proof"` 等免责句；把 64 位 SHA-256 原样展示给读者；在活跃文档里塞满自证清白的合规日志。
2. **无意义哈希与复算表演**：在普通脚本或清单里给代码和配置文件算 SHA-256（`program_sha256`、`sourceHashes`）；写单测把可编辑源码或界面文件锁死在哈希基线上；额外写 `verify_*.py` 复算脚本；用旧代码哈希拦住 `--overwrite` / `--resume`。
3. **防审计字段与 LLM Payload 污染**：往机器可读的 JSON 里塞 `limits`、`*_review`、`*_sha256` 等无人消费的免责字段，甚至直接传进下游 LLM 请求里——不仅浪费 token，还会让下游模型被输入语气传染、跟着打官腔。
4. **冗余多段校验流水线**：明明直接读取或查询就能完成，却强行拆成 `export -> prepare -> verify -> query` 并维护一堆中间审计清单。

## 默认工作方式（文字直接清理，代码先统计后治理）

改文字和改代码的影响面完全不同。除非你明确要求一次把代码也改掉，否则本技能默认拆成两步执行：

- **Phase A（默认）——直接清理文字 + 统计防御性代码**：
  - 直接清理 Markdown 文档、README、skill、提示词模板和独立对外文案中的免责叠甲、焦虑措辞、裸哈希与自证过程材料。
  - **默认不改动代码、Schema、LLM Payload 或测试**；而是先扫描统计，列出命中位置与数量、追踪跨语言消费方（如前端 `?v=` 缓存标签与测试断言），给出一份清晰的统计清单供你拍板。
- **Phase B（经你确认或明确指定后）——治理代码与 Schema**：
  - 先安全替换被复用的缓存/版本字段，再彻底删除无意义哈希、防审计字段、LLM Payload 里的防御性包装与多余校验环节，并运行真实端到端路径验证。

真正的安全边界不会被误删：路径穿越防护、密钥与隐私脱敏、破坏性或付费操作确认、有明确业务用途的鉴权签名/发布校验，以及用一句正面事实交代数据口径的说明都会保留。

## 安装与使用

本仓库根目录直接包含 `SKILL.md`，克隆后即可作为独立 Agent Skill 使用。

### Codex（`~/.codex/skills/`）

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git ~/.codex/skills/clean-chatgpt-flavor
```

### 项目级 Agent Skills（`.agents/skills/`）

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git .agents/skills/clean-chatgpt-flavor
```

### Claude Code（`~/.claude/skills/`）

```bash
git clone https://github.com/aaaAlexanderaaa/clean-chatgpt-flavor.git ~/.claude/skills/clean-chatgpt-flavor
```

### 常用调用方式

- **默认模式（直接清理文档文案，代码先出统计清单）**：
  > 用 `clean-chatgpt-flavor` 处理这个仓库：直接清理文档和文案里的免责叠甲，代码、哈希校验和防审计字段先给我出一份统计清单，先别改代码。
- **一步到位（显式要求连同代码一起治理）**：
  > 用 `clean-chatgpt-flavor` 一起清理文档以及 `src/`、`tests/` 里的无意义哈希校验和 LLM payload 包装，并跑通端到端验证。
- **写作或评审自检**：
  > 按 `clean-chatgpt-flavor` 检查一下这个 PR 有没有带入过度防御写法。

## 仓库结构

| 文件 | 作用 |
|---|---|
| [SKILL.md](SKILL.md) | 技能主文档：根因原则、两阶段默认工作流、四类特征、保留与删减对照表及自检清单 |
| [references/catalog.md](references/catalog.md) | 四类问题的具体改写对照样例，以及 Phase A 代码统计报告模板 |
| [agents/openai.yaml](agents/openai.yaml) | 可选 Codex 界面配置（`display_name`、`short_description`、`default_prompt`） |
| [README.md](README.md) / [README_CN.md](README_CN.md) | 独立分发用英文与中文说明 |
| [LICENSE](LICENSE) | MIT 开源协议 |
