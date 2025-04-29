# openai-codex-action
A simple github action implementation that invokes an agent based on a prompt and creates a PR with code changes

## Installation 

Simply copy the contents of action.yaml into your repository's `.github/workflows` folder and ensure your repository is configured to allow actions to have permissions to push.

Then invoke the action via `workflow_dispatch` with a prompt.

Make sure your repo declares the necessary secrets.

It's late, I'll improve this code/README tomorrow, or accept PRs doing so :)
