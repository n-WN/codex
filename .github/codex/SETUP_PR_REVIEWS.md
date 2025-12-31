# Setting Up Automatic PR Reviews

This guide shows you how to set up automatic PR reviews using Codex.

## Prerequisites

1. The `CODEX_OPENAI_API_KEY` secret must be configured in your repository settings
2. The workflow file `.github/workflows/pr-reviewer.yml` must be present

## Option 1: Manual Trigger Only (Default)

By default, the PR reviewer only runs when you manually add a label to a PR:

1. Open any Pull Request
2. Add one of these labels:
   - `codex-review` - For general code review
   - `codex-rust-review` - For Rust-specific review

3. Wait for the workflow to complete
4. Check the PR comments for the review
5. The label will be automatically removed

## Option 2: Enable Automatic Reviews

To have Codex automatically review all PRs when they're opened or updated:

1. Open `.github/workflows/pr-reviewer.yml`
2. Find the `on:` section at the top of the file
3. Uncomment the `opened` and `synchronize` lines:

**Before:**
```yaml
on:
  pull_request:
    types:
      # Uncomment these to enable automatic reviews:
      # - opened
      # - synchronize
      # Manual trigger via labels (always enabled):
      - labeled
```

**After:**
```yaml
on:
  pull_request:
    types:
      # Uncomment these to enable automatic reviews:
      - opened
      - synchronize
      # Manual trigger via labels (always enabled):
      - labeled
```

4. Commit and push this change
5. From now on, every new PR and PR update will be automatically reviewed

## Customizing Review Behavior

### For General Reviews

Edit `.github/codex/labels/codex-review.md` to customize what Codex looks for in general code reviews.

### For Rust Reviews

Edit `.github/codex/labels/codex-rust-review.md` to customize Rust-specific review guidelines.

## Testing the Setup

1. Create a test branch
2. Make a small change
3. Open a PR
4. If automatic reviews are enabled, wait for the review comment
5. If using manual trigger, add the `codex-review` label

## Troubleshooting

### Review doesn't run

- Check that `CODEX_OPENAI_API_KEY` is configured in repository secrets
- Verify the workflow file exists and is valid YAML
- Check the Actions tab for error messages

### Review doesn't post comment

- Check the workflow logs in the Actions tab
- Verify the `codex` step completed successfully
- Ensure `pull-requests: write` permission is granted

### Label not removed

- This is expected if the label was present before the PR was opened
- The label is only auto-removed when it's the trigger for the review

## Related Documentation

- [PR Review Documentation](../docs/pr-review.md) (English)
- [PR 审阅文档](../docs/pr-review-zh.md) (中文)
- [Codex Action](https://github.com/openai/codex-action)
