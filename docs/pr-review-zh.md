# 自动 PR 审阅

Codex 可以自动审阅 Pull Request 并发布评论，类似于 GitHub Copilot 的 PR 审阅功能。

## 快速开始

PR 审阅工作流已安装在 `.github/workflows/pr-reviewer.yml` 中，但需要配置：

1. **手动触发（默认启用）**：向任何 PR 添加 `codex-review` 或 `codex-rust-review` 标签
2. **自动触发（需要选择启用）**：编辑 `.github/workflows/pr-reviewer.yml` 并取消注释 `opened` 和 `synchronize` 事件类型

## 工作原理

PR 审阅工作流可以通过两种方式触发：

### 手动触发（默认）
- 向 PR 添加 `codex-review` 或 `codex-rust-review` 标签

### 自动触发（需要启用）
要启用自动审阅，请编辑 `.github/workflows/pr-reviewer.yml` 并取消注释以下行：
```yaml
on:
  pull_request:
    types:
      - opened      # 取消注释以自动审阅新 PR
      - synchronize # 取消注释以自动审阅 PR 更新
      - labeled
```

启用后，自动审阅将在以下情况下触发：
1. 打开新的 Pull Request 时
2. Pull Request 更新时（同步）
3. PR 被标记为 `codex-review` 或 `codex-rust-review` 标签时

## 自动审阅选择

工作流会智能选择适当的审阅类型：

- **Rust 审阅**：当检测到 `codex-rs/` 目录中有变更时自动应用
- **通用审阅**：应用于所有其他变更

## 手动触发审阅

您可以通过向 PR 添加标签来手动触发审阅：

- 添加 `codex-review` 标签进行通用代码审阅
- 添加 `codex-rust-review` 标签进行带有额外指南的 Rust 特定审阅

审阅发布后，标签会自动删除。

## 审阅类型

### 通用审阅 (`codex-review`)

提供简洁的审阅，包括：
- 变更摘要（1-2 句话）
- 突出显示关键观察的要点
- 对 PR 的友好反馈

审阅提示文件：`.github/codex/labels/codex-review.md`

### Rust 审阅 (`codex-rust-review`)

除了通用审阅反馈外，还包括：

**通用原则：**
- PR 动机和描述质量
- 每个 PR 的单一概念性焦点
- 代码重复检测

**代码组织：**
- 适当的 crate 放置
- 文件大小和组织
- 公共 API 结构

**测试质量：**
- 优先使用完整对象相等断言而非逐个字段比较
- 使用 `pretty_assertions::assert_eq` 获得更好的差异显示

**Rust 最佳实践：**
- 避免 `unsafe` 代码
- 惯用的 Rust 模式
- 按字母顺序排序的依赖项

审阅提示文件：`.github/codex/labels/codex-rust-review.md`

## 配置

工作流需要在仓库设置中配置 `CODEX_OPENAI_API_KEY` 密钥。

### 权限

工作流需要：
- `contents: read` - 读取仓库内容
- `pull-requests: write` - 发布审阅评论

## 自定义审阅提示

您可以通过编辑 `.github/codex/labels/` 中的提示文件来自定义审阅行为：

1. **codex-review.md** - 通用审阅说明
2. **codex-rust-review.md** - Rust 特定审阅指南

提示支持占位符：
- `{CODEX_ACTION_GITHUB_EVENT_PATH}` - GitHub 事件 JSON 的路径

## 示例工作流程

1. 开发者打开一个 PR
2. GitHub Actions 触发 `pr-reviewer` 工作流
3. Codex 根据选定的审阅类型分析变更
4. 自动将审阅评论发布到 PR
5. 开发者可以在 PR 中处理反馈或讨论

## 禁用自动审阅

要禁用自动审阅：

1. 删除或禁用 `.github/workflows/pr-reviewer.yml` 工作流
2. 或修改工作流的 `if` 条件以排除某些 PR（例如，按作者、分支模式或标签）

## 另请参阅

- [GitHub Action 集成](https://github.com/openai/codex-action)
- [自动化 Codex](../README.md#automating-codex)
- [贡献指南](./contributing.md)
