# Codex Label-Based Automation

This directory contains prompt files that define automated behaviors triggered by labels in issues and pull requests.

## Available Label Prompts

### For Issues

- **codex-attempt.md** - Triggered by `codex-attempt` label
  - Attempts to solve the reported issue
  - Creates a branch, commits fixes, and opens a pull request

- **codex-triage.md** - Triggered by `codex-triage` label
  - Troubleshoots whether the reported issue is valid
  - Provides a concise and respectful comment with findings

### For Pull Requests

- **codex-review.md** - Triggered by `codex-review` label or automatically on PR creation
  - Provides general code review feedback
  - Includes summary and bullet points with friendly tone

- **codex-rust-review.md** - Triggered by `codex-rust-review` label or automatically for Rust changes
  - Specialized review for Rust code changes
  - Includes additional checks for:
    - Code organization and crate placement
    - Test quality and assertion patterns
    - Rust-specific best practices
    - Pull request structure and documentation

## How It Works

1. **Trigger**: When a label is added to an issue or PR, or when certain events occur (like PR opened)
2. **Prompt Selection**: The workflow selects the appropriate prompt file based on the label or context
3. **Codex Execution**: The prompt is sent to Codex via the `openai/codex-action`
4. **Response**: Codex analyzes the issue/PR and generates a response
5. **Posting**: The response is automatically posted as a comment
6. **Cleanup**: The trigger label is removed after the action completes

## Prompt File Format

Prompt files use Markdown and can include placeholders:

- `{CODEX_ACTION_GITHUB_EVENT_PATH}` - Path to the GitHub event JSON file
- `{CODEX_ACTION_ISSUE_TITLE}` - Issue title (for issue workflows)
- `{CODEX_ACTION_ISSUE_BODY}` - Issue body (for issue workflows)

For PR workflows, the prompt receives:
- PR number, title, and body
- Author information
- Base and head branch information
- Full access to the repository and PR diff

## Creating New Label Prompts

To create a new label-based automation:

1. Create a new `.md` file in this directory (e.g., `my-custom-action.md`)
2. Write the prompt describing what Codex should do
3. Create or modify a workflow in `.github/workflows/` to trigger on your label
4. Reference your prompt file in the workflow

Example workflow snippet:

```yaml
- name: Read prompt
  id: read-prompt
  run: |
    PROMPT_CONTENT=$(cat ".github/codex/labels/my-custom-action.md")
    echo "prompt_content<<EOF" >> "$GITHUB_OUTPUT"
    echo "$PROMPT_CONTENT" >> "$GITHUB_OUTPUT"
    echo "EOF" >> "$GITHUB_OUTPUT"

- uses: openai/codex-action@main
  with:
    openai-api-key: ${{ secrets.CODEX_OPENAI_API_KEY }}
    prompt: ${{ steps.read-prompt.outputs.prompt_content }}
```

## Best Practices

- **Keep prompts focused**: Each prompt should have a single, clear purpose
- **Be specific**: Include detailed instructions and expectations
- **Provide context**: Reference relevant guidelines, standards, or examples
- **Use friendly tone**: Prompts should generate helpful, constructive feedback
- **Test thoroughly**: Try different scenarios before deploying to production

## Related Workflows

- `.github/workflows/issue-labeler.yml` - Automatically suggests labels for new issues
- `.github/workflows/issue-deduplicator.yml` - Detects duplicate issues
- `.github/workflows/pr-reviewer.yml` - Automatically reviews pull requests

## Documentation

- [PR Review Documentation](../../docs/pr-review.md)
- [PR Review Documentation (中文)](../../docs/pr-review-zh.md)
- [Codex Action GitHub Repository](https://github.com/openai/codex-action)
