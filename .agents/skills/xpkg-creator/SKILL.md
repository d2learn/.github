---
name: xpkg-creator
description: Create, test, and submit XPackage (XPKG) package descriptors with strict install/config hook separation, XVM mapping, and minimal system impact.
---

# XPKG-creator Skill

用于指导贡献者（或 AI 代理）创建、测试并提交 **XPackage (XPKG)** 包描述文件，遵循 subOS / XLinux 的环境隔离与多版本共存原则。

---

## 0. 适用场景
当你需要：
- 新增一个工具到 XPackage 包仓库；
- 维护已有 XPKG 描述文件（升级版本、修复安装/注册逻辑）；
- 产出可审计、可复现、最小系统改动的包规范文档。

---

## 1. 参考工具与链接

### XLinks 工具（建议结合使用）
- GitHub: https://github.com/d2learn/xlinks

用途建议：
- 通过仓库文档了解工具链接、映射与注册的推荐做法。
- 结合 XVM 使用，减少直接改写系统路径/系统配置的需求。

安装入口建议：
1. 优先阅读仓库 README/文档中的官方安装说明。
2. 若有预构建二进制发布（Release），优先采用二进制安装。
3. 避免在包安装阶段引入高耦合的系统级依赖。

---

## 2. XPKG 包描述文件：格式与规范（建议模板）

> 说明：以下为推荐结构，字段名请以目标包仓库当前规范为准。

```yaml
name: foo
version: 1.2.3
summary: Short summary for users
description: |
  Long description...
license: MIT
homepage: https://example.com/foo
source:
  url: https://example.com/foo/releases/foo-1.2.3-linux-amd64.tar.gz
  sha256: <checksum>
arch:
  - amd64
  - arm64
bin:
  - foo
hooks:
  install:
    - <install steps>
  config:
    - <register/mapping steps>
```

编写主张：
1. **可复现**：固定版本、固定下载源、校验哈希。
2. **可移植**：避免写死宿主机特定路径；支持常见架构。
3. **最小权限**：只在包作用域内落盘，不扩大系统影响面。
4. **可审计**：关键动作（下载、解压、映射、清理）可追踪。

---

## 3. `install` 与 `config` hook 的职责边界

### install hook（安装主体）
只处理“把软件本体放到包管理可控目录”这件事：
- 下载/解压/拷贝二进制或运行时文件。
- 目录重排、权限设置（仅包内部目录）。
- 不做系统级注册，不改全局 shell/profile，不写系统服务。

### config hook（注册与映射）
只处理“让工具在 subOS/XVM 体系内可发现、可使用”：
- 在 XVM 中注册命令映射与版本元数据。
- 建立环境隔离所需的软链接/索引（限工具体系内部）。
- 保证多版本共存：不同版本并排安装、按需切换。

### 禁止/不推荐动作
- 直接覆盖 `/usr/bin`、`/usr/local/bin` 等全局路径。
- 修改系统级配置导致宿主环境不可逆变化。
- 在 config 阶段重新下载大体积构件或执行编译。

---

## 4. 包实现策略（优先预构建二进制）

1. **优先二进制分发**
   - 若上游有稳定 release 产物，优先使用。
   - 好处：安装快、依赖少、跨环境一致性更高。

2. **XVM 映射优先于系统改动**
   - 安装后通过 XVM 注册命令可见性。
   - 不破坏 subOS 的版本视图与隔离模型。

3. **最小化系统修改**
   - 不写全局 profile。
   - 不污染系统包管理数据库。
   - 卸载时可完整回收。

---

## 5. 写完包后的测试流程（必须执行）

### A. 安装测试
- 执行包安装。
- 确认安装过程无异常，目标文件存在于预期目录。

### B. 搜索/发现测试
- 使用包管理或工具索引命令确认包可被搜索到。
- 检查版本信息、包元数据展示正确。

### C. 命令可用性测试
- 调用工具主命令（如 `foo --version`、`foo help`）。
- 验证通过 XVM/subOS 映射后命令可用。

### D. 卸载与清理测试
- 卸载包后再次检查：
  - 可执行文件与注册信息是否移除。
  - 残留软链接/索引是否清理干净。
  - 不影响其他版本与其他包。

### E. 回归检查（可选但推荐）
- 重复“安装 -> 使用 -> 卸载”至少一轮。
- 在至少两种环境/架构中抽样验证（如 amd64/arm64）。

---

## 6. 向官方包仓库提交 PR 的要求

提交者（人或 AI）在 PR 中应包含：

1. **包简介与用途**
   - 这个包是什么、解决什么问题、目标用户是谁。

2. **变更范围**
   - 新增/修改了哪些文件。
   - 关键逻辑（install/config）如何工作。

3. **安装/卸载行为说明**
   - 安装写入了哪些目录。
   - 卸载会清理哪些对象。
   - 是否涉及系统改动，若有必须说明必要性与最小化策略。

4. **验证证据**
   - 本地执行了哪些测试（安装、搜索、命令、卸载）。
   - 验证平台与架构（至少说明实际测试环境）。
   - 异常场景与已知限制。

5. **安全与可维护性说明**
   - 版本和 checksum 来源可信。
   - 不破坏 subOS/XLinux 的隔离与多版本共存模型。

---

## 7. PR 描述建议模板

```markdown
## What
- Add/Update package: <name>
- Version: <version>
- Purpose: <why this package>

## How
- install hook: <key actions>
- config hook: <key actions>
- system impact: <none/minimal + details>

## Validation
- [x] Install succeeded
- [x] Search metadata visible
- [x] Command works (`<cmd --version>`)
- [x] Uninstall clean
- Platforms: <e.g. linux/amd64, linux/arm64>

## Notes
- Known limitations: <if any>
```

---

## 8. 快速检查清单（提交前）
- [ ] 固定版本与哈希。
- [ ] install/config 职责分离清晰。
- [ ] 优先二进制，避免不必要编译。
- [ ] 通过 XVM 注册，不污染系统。
- [ ] 安装/搜索/命令/卸载测试有记录。
- [ ] PR 描述完整、可审查、可复现。
