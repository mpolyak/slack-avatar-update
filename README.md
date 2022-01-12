# slack-avatar-update
Automated update of my [Slack avatar](https://github.com/mpolyak/slack-abstract), using GitHub Actions.

## Execution

The workflow updating the avatar is [scheduled](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#scheduled-events) to run every 7 minutes, but in practice has unpredictable execution times.

It is also [triggered](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#repository_dispatch) to run by a [Cloudflare Worker](https://workers.cloudflare.com/) using a [cron trigger](https://developers.cloudflare.com/workers/platform/cron-triggers) every 30 minutes:

```javascript
addEventListener("scheduled", (event) =>
    event.waitUntil(triggerUpdate()));

async function triggerUpdate() {
    const resp = await fetch(
        "https://api.github.com/repos/mpolyak/slack-avatar-update/dispatches",
        {
            method: "POST",
            headers: {
                "Accept": "application/vnd.github.v3+json",
                "Authorization": "Bearer " + GITHUB_PAT,
                "User-Agent": "fetch",
            },
            body: '{"event_type":"update"}',
        });

    if (resp.status !== 204) {
        return Promise.reject(resp.statusText);
    }

    return Promise.resolve("ok");
}
```

## License
[MIT](LICENSE)
