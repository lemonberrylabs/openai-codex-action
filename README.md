# OpenAI Codex Code Generator Action

A GitHub Action that uses OpenAI's Codex to automatically generate code changes and create pull requests based on natural language prompts. This action allows you to leverage AI to assist with code modifications, refactoring, and improvements.

## Features

- 🤖 Uses OpenAI's Codex to generate code changes from natural language prompts
- 🔄 Creates a new branch with the changes
- 📝 Automatically opens a pull request with detailed description
- 🎛️ Configurable approval modes (suggest, auto-edit, full-auto)
- 🔒 Secure handling of tokens and API keys

## Prerequisites

1. An OpenAI API key with access to Codex
2. GitHub token with permissions to create branches and pull requests

## Usage

### Basic Example

```yaml
name: AI Code Generation
on:
  workflow_dispatch:
    inputs:
      prompt:
        description: 'What changes would you like the AI to make?'
        required: true

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: lemonberrylabs/openai-codex-action@main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          provider_api_key: ${{ secrets.OPENAI_API_KEY }}  # Uses OpenAI by default
          prompt: ${{ github.event.inputs.prompt }}
          model: 'o4-mini'  # Optional: defaults to 'o4-mini'
```

### Complete Example with All Options

```yaml
name: AI Code Generation
on:
  workflow_dispatch:
    inputs:
      prompt:
        description: 'What changes would you like the AI to make?'
        required: true
      branch:
        description: 'Branch to base changes on'
        default: 'main'
        required: false
      mode:
        description: 'AI approval mode'
        default: 'full-auto'
        required: false
        type: choice
        options:
          - suggest
          - auto-edit
          - full-auto
      model:
        description: 'AI model to use'
        default: 'o4-mini'
        required: false
      provider:
        description: 'AI provider to use'
        default: 'openai'
        required: false
        type: choice
        options:
          - openai
          - openrouter
          - gemini
          - ollama
          - mistral
          - deepseek
          - xai
          - groq

jobs:
  generate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: lemonberrylabs/openai-codex-action@main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          provider: ${{ github.event.inputs.provider || 'openai' }}
          provider_api_key: ${{ secrets.OPENAI_API_KEY }}  # Will use appropriate provider's key based on provider input
          provider_base_url: ${{ secrets.PROVIDER_BASE_URL }}  # Optional: Only needed for providers other than OpenAI
          prompt: ${{ github.event.inputs.prompt }}
          branch_name: ${{ github.event.inputs.branch || 'main' }}
          approval_mode: ${{ github.event.inputs.mode || 'full-auto' }}
          model: ${{ github.event.inputs.model || 'o4-mini' }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `github_token` | GitHub token for creating branches and PRs | Yes | N/A |
| `provider_api_key` | API key for the chosen provider (defaults to OpenAI) | Yes | N/A |
| `prompt` | The natural language prompt describing desired changes | Yes | N/A |
| `branch_name` | The branch to base changes on | No | `'main'` |
| `approval_mode` | How the AI should handle changes (`suggest`, `auto-edit`, or `full-auto`) | No | `'full-auto'` |
| `model` | The model to use for code generation | No | `'o4-mini'` |
| `provider` | The AI provider to use | No | `'openai'` |
| `provider_base_url` | Base URL for the AI provider API endpoint. Only needed for providers other than OpenAI. | No | N/A |

## Outputs

| Output | Description |
|--------|-------------|
| `changes_detected` | Boolean indicating if any changes were made |
| `new_branch` | Name of the created branch (if changes were detected) |
| `pr_url` | URL of the created pull request (if changes were detected) |

## Approval Modes

- `suggest`: AI will suggest changes but not apply them
- `auto-edit`: AI will make changes but require human approval
- `full-auto`: AI will make changes and create PR automatically

## Security

This action requires two sensitive tokens:

1. `github_token`: Used for Git operations and PR creation
   - Recommended: Use `${{ secrets.GITHUB_TOKEN }}`
   - Permissions needed: `contents: write` and `pull-requests: write`

2. `openai_api_key`: Used for accessing OpenAI's Codex
   - Must be added as a repository secret
   - Never expose this key in your workflow files

## Example Prompts

- "Refactor the main function in src/index.js to improve readability"
- "Add input validation to all public methods in the UserService class"
- "Update the error handling in the API routes to use the new ErrorHandler"
- "Convert all callback-based functions to use async/await"

## Limitations

- The action uses the experimental @openai/codex CLI
- Changes are limited to what Codex can understand and modify
- Large codebases may require more specific prompts
- Some changes may require human review before merging

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see the [LICENSE](LICENSE) file for details.

## Providers

The action supports multiple AI providers through the OpenAI Chat Completions API compatibility layer. Available providers include:

- `openai` (default): Uses OpenAI's models directly
- `openrouter`: Access to multiple AI models through OpenRouter
- `gemini`: Google's Gemini models
- `ollama`: Local or self-hosted Ollama models
- `mistral`: Mistral AI's models
- `deepseek`: DeepSeek's code models
- `xai`: xAI's models
- `groq`: Groq's high-performance inference
- Custom providers that are compatible with the OpenAI API

### Using Alternative Providers

To use a provider other than OpenAI:

1. Set the `provider` input to your chosen provider
2. Set `provider_api_key` to your provider's API key
3. For custom providers, set the `provider_base_url` to your API endpoint

Example using Mistral:
```yaml
- uses: lemonberrylabs/openai-codex-action@main
  with:
    provider: 'mistral'
    provider_api_key: ${{ secrets.MISTRAL_API_KEY }}
    # ... other inputs ...
```

Example using a custom provider:
```yaml
- uses: lemonberrylabs/openai-codex-action@main
  with:
    provider: 'custom'
    provider_api_key: ${{ secrets.CUSTOM_API_KEY }}
    provider_base_url: 'https://your-provider-api-base-url'
    # ... other inputs ...
```

### Environment Variables

The action automatically sets up the appropriate environment variables based on your provider:

- For OpenAI (default): Sets `OPENAI_API_KEY`
- For other providers: Sets `<PROVIDER>_API_KEY` and `<PROVIDER>_BASE_URL` if provided

You don't need to manage these environment variables directly - the action handles this based on the inputs you provide.
