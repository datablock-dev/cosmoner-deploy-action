# Cosmoner Deploy

Deploy an image app on [Cosmoner](https://cosmoner.com) from a GitHub Actions
workflow, and wait for it to go live. An image app runs an image from a
Cosmoner registry; apps built from a repository deploy by pushing to their
branch instead.

It runs [`cosmoner deploy`](https://github.com/datablock-dev/cosmoner-sdk/tree/main/cli),
so it behaves exactly as the CLI does on your own machine. The runner needs
Node.js 20 or later, which GitHub-hosted runners already have.

To upload a folder to a web hosting site, use
[cosmoner-upload-action](https://github.com/datablock-dev/cosmoner-upload-action).

## Usage

```yaml
- uses: datablock-dev/cosmoner-deploy-action@v1
  with:
    api-key: ${{ secrets.COSMONER_API_KEY }}
    project-id: ${{ vars.COSMONER_PROJECT_ID }}
    app: web
    tag: ${{ github.sha }}
```

With neither `tag` nor `digest`, the image the app already names is pulled
again, which picks up a tag that was pushed over.

The API key needs `apps:read` and `apps:write`.

## Inputs

| Input | Default | |
| --- | --- | --- |
| `api-key` | | Cosmoner API key. Pass it from a secret. |
| `project-id` | | Project the app is in. |
| `app` | | The app's name or id. |
| `tag` | | Image tag to deploy. |
| `digest` | | Exact image, `sha256:<64 hex>`, instead of a tag. |
| `wait` | `true` | Wait for the rollout to go live. |
| `timeout` | `600` | Seconds to wait. A timeout stops the wait, not the deploy. |
| `cli-version` | `0.3` | `@cosmoner/cli` version or range to run. |

The step fails when the deploy fails or times out, and when `app` is missing.

## License

[MIT](LICENSE)
