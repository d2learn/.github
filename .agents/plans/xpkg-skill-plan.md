# XPKG Creator Skill 实施计划

## 1. 项目初步分析
- 当前仓库为社区文档型仓库（`README.md`、`profile/README.md`），适合作为流程规范与协作文档的承载位置。
- 目标不是实现运行时代码，而是沉淀一套可复用的 **XPackage（XPKG）包描述编写与提交流程**。
- 由于本仓库内容轻量，新增 `.agents` 目录用于承载 AI/贡献者协作资产（计划、任务、技能、报告）可避免污染现有对外文档。

## 2. 目标
1. 在 `.agents/skills` 下创建 `XPKG-creator` skill。
2. skill 必须覆盖：
   - XPKG 包描述文件格式与规范。
   - `install` 与 `config` hook 的职责边界。
   - 通过 XVM 做工具映射，尽量最小化系统改动。
   - 优先使用预构建二进制包，保证安装轻量与可复现。
   - 包测试流程（安装/搜索/命令可用性/卸载清理）。
   - 向官方包仓库提交 PR 的说明要求与证据清单。
3. 在 `.agents/plans`、`.agents/tasks`、`.agents/docs` 中补齐计划、任务拆解、执行报告。

## 3. 交付物
- `.agents/plans/xpkg-skill-plan.md`
- `.agents/tasks/*.md`（可执行任务拆解）
- `.agents/skills/xpkg-creator/SKILL.md`
- `.agents/docs/xpkg-skill-report.md`

## 4. 执行步骤
1. 梳理仓库现状与需求约束。
2. 定义 skill 结构：概念、规范模板、hook 边界、最佳实践、测试与提交流程。
3. 补齐计划与任务文档，确保每个任务包含目标、实现内容、验收标准、测试项。
4. 完成执行报告，记录实现结果与后续建议。
5. 完成 git 提交与 PR 描述。

## 5. 验收标准
- 目录结构完整且命名统一。
- skill 内容可直接指导贡献者创建并验证一个 XPKG 包。
- 明确强调“最小系统修改、XVM 映射、隔离共存”的核心原则。
- 包含 XLinks 工具仓库链接与安装入口提示。
- 文档之间（计划/任务/报告）语义一致、可追溯。
