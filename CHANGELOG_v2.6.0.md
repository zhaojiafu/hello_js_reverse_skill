# v2.6.0 — 签名型反爬兼容对齐（2026-04-18）

对齐 camoufox-reverse MCP **v0.5.0**（签名型反爬兼容改造版）。

## 核心主题

v2.5.0 对 MCP v0.4.0 的四板斧做了完整适配，但**对四板斧各自的适用反爬类型没有区分**。
v0.5.0 明确了一件事：前三板斧（Hook/插桩/日志）对签名型反爬（瑞数/Akamai）**不可用**
——它们改变 VMP 眼里的环境，破坏 cookie 签名，导致挑战永远不过（症状：`redirect_chain`
反复 412）。v2.6.0 把这个判断吸收进 Skill 的顶层决策框架。

## 变更清单

### 新增（文档层面）
- `SKILL.md` 新增顶层章节「反爬类型识别与工具选择」，放在 Phase 0.5 之前
  - 三分法总览表（签名型/行为型/纯混淆）
  - 识别标准动作（`navigate` 不加 hook 看 `redirect_chain`）
  - 三条反模式警告
- `SKILL.md` 经验法则 28-32 条（签名型只走源码插桩 / pre_inject_hooks 正确定位 /
  transparent 模式定位 / MCP 侧 AST 能用 / 反爬类型识别是 Phase 0 的 Phase 0）
- `SKILL.md` 错误处理新增「观察者效应」专项排查
- `references/jsvmp-analysis.md` 新增前置反爬类型识别树
- `references/jsvmp-analysis.md` 新增「签名型 JSVMP 专属路径」小节
- `references/jsvmp-source-instrumentation.md` 新增「AST 模式健康诊断」小节
- `references/mcp-cookbook.md` 新增 transparent 模式与 MCP 侧 AST 用法示例
- `CHANGELOG_v2.6.0.md`（本文件）

### 修改（文档层面）
- `SKILL.md` frontmatter description 吸收 v2.6.0 定位
- `SKILL.md` 对 `hook_jsvmp_interpreter` 增加 `mode` 参数说明和警告
- `SKILL.md` 对 `instrument_jsvmp_source` 增加 MCP 侧 AST 迁移说明和 `fallback_on_error` 参数
- `SKILL.md` 对 `pre_inject_hooks` 参数增加"签名型不可用"警告
- `SKILL.md` 四板斧总览表加"适用反爬类型"列
- `SKILL.md` 路径 A 决策树前加反爬类型识别前置
- `SKILL.md` 场景 9（反检测站点）按反爬类型分档重写
- `SKILL.md` 场景 11（源码级插桩 8 步）加签名型前置检查
- `README.md` 工具数/能力介绍更新到 v0.5.0
- `cases/README.md` 案例索引加反爬类型标签

### 未改动（保持原样）
- 合规授权框架、四条第一原则、浏览器连接策略
- Phase 0 / 0.5 / 1 / 2 / 3 / 4 / 5 总体骨架
- 路径 A 四板斧原有内容、路径 B 环境伪装六步法原有内容
- `templates/` 下所有代码模板
- `references/jsdom-env-patches.md`
- `references/hook-techniques.md` / `references/anti-debug.md` / 其他 references
- 所有已有 cases

## 不兼容变化

无。v0.5.0 的 `mode="proxy"` 是 `hook_jsvmp_interpreter` 的默认值（向后兼容），
用户已有调用不需要改。`instrument_jsvmp_source(mode="ast")` 的默认行为变了
（页面内 Acorn → MCP 侧 esprima），但 API 签名不变，对文档读者透明。
