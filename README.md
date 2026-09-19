# Cosmoner GitHub Action

Deploy an image app, or upload a folder to a web hosting site, on
[Cosmoner](https://cosmoner.com) from a GitHub Actions workflow.

It runs [`@cosmoner/cli`](https://github.com/datablock-dev/cosmoner-sdk/tree/main/cli),
so it behaves exactly like `cosmoner deploy` and `cosmoner upload` do on your
own machine. The runner needs Node.js 20 or later, which GitHub-hosted runners
already have.

## Upload to web hosting

Copies a folder to a site over SFTP. The SFTP login is fetched with the API key,
so the key is the only secret you store.

```yaml
- uses: actions/checkout@v7

- run: npm ci && npm run build

- uses: datablock-dev/cosmoner-action@v1
  with:
    command: upload
    api-key: ${{ secrets.COSMONER_API_KEY }}
    project-id: ${{ vars.COSMONER_PROJECT_ID }}
    site: my-site
    path: dist
    delete: true
    host-key: SHA256:PfqYSl1pbMjfMKAbcmjzGZ0t1kpuCZ2mtymdyLu9HwA
```

Every file in `path` is uploaded into the folder the site's own hostname serves,
unless `remote-path` names another, such as `/shop.example.com/public_html`.
With `delete: true`, files on the site that are not in `path` are removed once
the upload has finished. `.git` folders and symlinks are never uploaded.

`host-key` pins the SFTP gateway's key: a server presenting any other key is
refused before the password is sent. The value above is the gateway's current
key.

The API key needs `hosting:read`.

## Deploy an image app

Rolls an app that runs an image from a Cosmoner registry onto a new tag, and
waits for it to go live.

```yaml
- uses: datablock-dev/cosmoner-action@v1
  with:
    command: deploy
    api-key: ${{ secrets.COSMONER_API_KEY }}
    project-id: ${{ vars.COSMONER_PROJECT_ID }}
    app: web
    tag: ${{ github.sha }}
```

The API key needs `apps:read` and `apps:write`.

## Inputs

| Input | Used by | Default | |
| --- | --- | --- | --- |
| `command` | both | | `deploy` or `upload`. |
| `api-key` | both | | Cosmoner API key. Pass it from a secret. |
| `project-id` | both | | Project the app or site is in. |
| `app` | deploy | | The app's name or id. |
| `tag` | deploy | | Image tag to deploy. |
| `digest` | deploy | | Exact image, `sha256:<64 hex>`, instead of a tag. |
| `wait` | deploy | `true` | Wait for the rollout to go live. |
| `timeout` | deploy | `600` | Seconds to wait. |
| `site` | upload | | The hosting site's name or id. |
| `path` | upload | | Local folder whose contents are uploaded. |
| `remote-path` | upload | the site's own folder | Folder on the site to upload into. |
| `delete` | upload | `false` | Remove files on the site that are not in `path`. |
| `dry-run` | upload | `false` | List what would change without changing it. |
| `host-key` | upload | | SHA256 fingerprint(s) of the gateway's host key, comma-separated. |
| `cli-version` | both | `0.3` | `@cosmoner/cli` version or range to run. |

The step fails when the deploy or upload fails, and when an input is missing.

## License

[MIT](LICENSE)
