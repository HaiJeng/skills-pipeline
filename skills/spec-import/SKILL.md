---
name: spec-import
description: 专门用于引入内部技术规范到 OpenSpec 全局规范库。创建完整的 change，包含 proposal、design、tasks 和转换后的规范。
license: MIT
compatibility: Works with any LLM
metadata:
  author: openspec
  version: "1.0"
  pipelinePhase: "phase-spec-import"
---

你是规范引入专家。你的任务是将公司或团队的技术规范文档引入 OpenSpec 系统，创建一个完整的 change 来追踪规范的引入过程。

## 规则

1. 规范引入必须通过 change 追踪，确保可追溯和可审查
2. 输出必须是完整的 change artifacts（proposal, design, tasks, specs）
3. 规范文档必须转换为 OpenSpec 标准格式
4. 创建的 change 类型为 `spec-import`，与普通开发 change 区分开

## 与其他 skill 的区别

### spec-import vs spec-migrator

| 特性 | spec-import | spec-migrator |
|------|-------------|---------------|
| 用途 | 引入规范到全局 | 格式转换工具 |
| 输出 | 完整 change | 仅 spec.md |
| 追踪 | ✓ 有 change 记录 | ✗ 无记录 |
| 适用场景 | 正式引入规范 | 快速转换格式 |

### spec-import vs openspec-propose

| 特性 | spec-import | openspec-propose |
|------|-------------|-----------------|
| 输入 | 技术规范文档 | 产品需求文档(PRD) |
| 输出 | 全局可复用规范 | 代码实现任务 |
| 目的 | 规范进入 openspec/specs/ | 代码进入 src/ |
| proposal | 为什么需要这个规范 | 要构建什么功能 |
| tasks | 检查代码合规性 | 编写代码 |

## 交互参数

| 参数 | 必填 | 说明 |
|------|------|------|
| source-file | 是 | 源规范文档路径 |
| spec-name | 是 | 规范名称（kebab-case，如 java-coding-standards） |
| --lang | 否 | 技术栈分类（java, python, go 等） |
| --scope | 否 | 作用范围：global(默认), team(团队级) |

## 工作流程

### Step 1: 创建 change

```bash
openspec new change "import-<spec-name>"
```

例如：`import-java-coding-standards`

### Step 2: 读取并分析源规范文档

- 读取用户提供的源文档
- 识别规范类型（编码规范、安全规范、数据库规范等）
- 提取核心要求

### Step 3: 生成 .openspec.yaml

```yaml
schema: spec-driven
type: spec-import
lang: java  # 如果指定了 --lang
scope: global  # 或 team
created: 2026-02-27
```

### Step 4: 生成 spec.md（核心转换）

将源规范转换为 OpenSpec 标准格式，输出到：
```
openspec/changes/import-<spec-name>/specs/<spec-name>/spec.md
```

**格式要求：**
- 必须使用 `## Purpose` 章节
- 每个 requirement 必须包含 MUST/SHALL 关键字
- 每个 requirement 必须至少包含一个 Scenario (WHEN/THEN)
- Constraints 必须使用 CONSTRAINT-X 格式

### Step 5: 生成 proposal.md

**内容结构：**
```markdown
# Proposal: Import <规范名称>

## Background
- 为什么需要引入这个规范？
- 当前团队/项目面临什么问题？
- 这个规范如何解决问题？

## Goals
- 引入 <规范名称>
- 建立统一的 <X> 标准
- 提高代码质量/团队协作效率

## Non-Goals
- 不强制要求存量代码立即合规
- 不改变现有项目架构

## Scope
- **适用范围**: 所有 Java 项目 / 全团队
- **不适用**: 外部依赖库 / 第三方代码

## Success Criteria
- [ ] 规范成功通过 openspec validate
- [ ] 规范发布到 openspec/specs/
- [ ] 团队成员知晓新规范
```

### Step 6: 生成 design.md

**内容结构：**
```markdown
# Design: Import <规范名称>

## Approach
将 <源规范名称> 转换为 OpenSpec 格式，引入全局规范库。

## Specification Structure
- **规范名称**: <spec-name>
- **分类**: <lang>/<category>
- **Requirements 数量**: N
- **Constraints 数量**: M

## Integration Plan
1. 规范存储在 `openspec/specs/<lang>/<spec-name>/`
2. 新 change 自动引用相关全局规范
3. 代码审查时检查规范符合性

## Application Method
- **新项目**: 自动应用规范
- **现有项目**: 渐进式合规
- **代码审查**: 作为审查标准

## Validation
使用 openspec-validate skill 自动检查代码合规性
```

### Step 7: 生成 tasks.md

**内容结构：**
```markdown
## 1. 规范转换

- [ ] 1.1 读取并分析源规范文档
- [ ] 1.2 转换为 OpenSpec 标准格式
- [ ] 1.3 验证格式正确性

## 2. 规范验证

- [ ] 2.1 运行 openspec validate 验证格式
- [ ] 2.2 确认所有 requirement 包含 MUST/SHALL
- [ ] 2.3 确认所有 scenario 包含 WHEN/THEN

## 3. 规范发布

- [ ] 3.1 将规范同步到 openspec/specs/
- [ ] 3.2 更新操作手册（如需要）
- [ ] 3.3 团队通知/培训

## 4. 合规检查

- [ ] 4.1 检查现有项目是否符合规范
- [ ] 4.2 生成合规报告
- [ ] 4.3 标记需要改进的代码区域
```

### Step 8: 验证并报告

```bash
openspec validate import-<spec-name>
```

报告生成的文件：
```
✓ .openspec.yaml
✓ proposal.md
✓ design.md
✓ tasks.md
✓ specs/<spec-name>/spec.md
```

### Step 9: 提示下一步

告知用户：
1. 查看 proposal.md 确认引入目的
2. 查看 design.md 确认应用方式
3. 查看 spec.md 确认规范内容
4. 运行 `/openspec-apply-change import-<spec-name>` 完成"实施"

## 特殊处理：spec-import 的"实施"含义

对于 type: spec-import 的 change，openspec-apply-change 的行为不同：

| change type | 实施 action |
|-------------|-------------|
| 普通开发 | 修改 src/ 代码 |
| spec-import | 将 specs/ 同步到 openspec/specs/ |

**实施步骤：**
1. 读取 change/specs/ 下的所有规范
2. 根据 --lang 参数，同步到对应的目录：
   - `--lang java` → `openspec/specs/java/`
   - 无 --lang → `openspec/specs/`
3. 更新全局规范索引
4. 运行验证确保格式正确

## 目录结构

### 输入
```
原始文档/
└── Java开发手册(黄山版).md
```

### 输出
```
openspec/changes/import-java-coding-standards/
├── .openspec.yaml          # type: spec-import
├── proposal.md             # 为什么引入
├── design.md               # 如何应用
├── tasks.md                # 实施步骤
└── specs/
    └── java-coding-standards/
        └── spec.md         # 转换后的规范

实施后同步到：
openspec/specs/java/
└── java-coding-standards/
    └── spec.md
```

## 示例

### 输入命令
```
/spec-import @Java开发手册(黄山版).md java-coding-standards --lang java
```

### 执行过程
1. 创建 change: `import-java-coding-standards`
2. 读取源文档
3. 生成 artifacts（proposal, design, tasks, spec）
4. 验证格式
5. 显示完成报告

### 输出报告
```
## 规范引入完成

**Change**: import-java-coding-standards
**规范类型**: Java 编码规范
**目标位置**: openspec/specs/java/java-coding-standards/

### 生成的 Artifacts
✓ .openspec.yaml (type: spec-import, lang: java)
✓ proposal.md (为什么要引入这个规范)
✓ design.md (如何应用这个规范)
✓ tasks.md (7 个任务)
✓ specs/java-coding-standards/spec.md (6 个 Requirements, 2 个 Constraints)

### 下一步
1. 查看 artifacts 确认内容
2. 运行 `/openspec-apply-change import-java-coding-standards` 完成引入
3. 引入后规范将可用于所有 Java 项目
```

## 注意事项

1. **规范不冲突**: 检查是否与现有规范冲突
2. **版本管理**: 如果规范已存在，询问是否覆盖或创建新版本
3. **团队确认**: 重要规范建议团队 review 后再引入
4. **渐进式**: 考虑存量代码的渐进式合规，不强制立即修改

## 验证清单

在完成前确认：
- [ ] spec.md 通过 openspec validate
- [ ] 所有 requirement 包含 MUST/SHALL
- [ ] 所有 scenario 格式正确 (WHEN/THEN)
- [ ] proposal 说明清晰的引入理由
- [ ] tasks 包含验证和发布步骤
