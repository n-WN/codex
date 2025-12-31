# Automatic PR Review

Codex can automatically review pull requests and post comments, similar to GitHub Copilot's PR review feature.

## Quick Start

The PR review workflow is installed in `.github/workflows/pr-reviewer.yml` but requires configuration:

1. **Manual trigger (enabled by default)**: Add a `codex-review` or `codex-rust-review` label to any PR
2. **Automatic trigger (opt-in)**: Edit `.github/workflows/pr-reviewer.yml` and uncomment the `opened` and `synchronize` event types

## How It Works

The PR review workflow can trigger in two ways:

### Manual Trigger (Default)
- Add the `codex-review` or `codex-rust-review` label to a PR

### Automatic Trigger (Opt-in)
To enable automatic reviews, edit `.github/workflows/pr-reviewer.yml` and uncomment these lines:
```yaml
on:
  pull_request:
    types:
      - opened      # Uncomment to review new PRs automatically
      - synchronize # Uncomment to review PR updates automatically
      - labeled
```

Once enabled, automatic reviews will trigger when:
1. A new pull request is opened
2. A pull request is updated (synchronized)
3. A PR is labeled with `codex-review` or `codex-rust-review`

## Automatic Review Selection

The workflow intelligently selects the appropriate review type:

- **Rust Review**: Automatically applied when changes are detected in `codex-rs/` directory
- **General Review**: Applied for all other changes

## Manual Review Triggers

You can manually trigger a review by adding labels to your PR:

- Add `codex-review` label for a general code review
- Add `codex-rust-review` label for a Rust-specific review with additional guidelines

The labels are automatically removed after the review is posted.

## Review Types

### General Review (`codex-review`)

Provides a concise review with:
- Summary of changes (1-2 sentences)
- Bullet points highlighting key observations
- Friendly feedback on the PR

Review prompt: `.github/codex/labels/codex-review.md`

### Rust Review (`codex-rust-review`)

In addition to general review feedback, includes:

**General Principles:**
- PR motivation and body quality
- Single conceptual focus per PR
- Code duplication detection

**Code Organization:**
- Appropriate crate placement
- File size and organization
- Public API structure

**Test Quality:**
- Prefer full object equality assertions over piecemeal comparisons
- Use `pretty_assertions::assert_eq` for better diffs

**Rust Best Practices:**
- Avoid `unsafe` code
- Idiomatic Rust patterns
- Alphabetically sorted dependencies

Review prompt: `.github/codex/labels/codex-rust-review.md`

## Configuration

The workflow requires the `CODEX_OPENAI_API_KEY` secret to be configured in your repository settings.

### Permissions

The workflow needs:
- `contents: read` - To read repository contents
- `pull-requests: write` - To post review comments

## Customizing Review Prompts

You can customize the review behavior by editing the prompt files in `.github/codex/labels/`:

1. **codex-review.md** - General review instructions
2. **codex-rust-review.md** - Rust-specific review guidelines

The prompts support placeholders:
- `{CODEX_ACTION_GITHUB_EVENT_PATH}` - Path to the GitHub event JSON

## Example Workflow

1. Developer opens a PR
2. GitHub Actions triggers the `pr-reviewer` workflow
3. Codex analyzes the changes based on the selected review type
4. A review comment is automatically posted to the PR
5. Developers can address the feedback or discuss it in the PR

## Disabling Automatic Reviews

To disable automatic reviews:

1. Remove or disable the `.github/workflows/pr-reviewer.yml` workflow
2. Or modify the workflow's `if` condition to exclude certain PRs (e.g., by author, branch pattern, or labels)

## See Also

- [GitHub Action Integration](https://github.com/openai/codex-action)
- [Automating Codex](../README.md#automating-codex)
- [Contributing Guidelines](./contributing.md)
