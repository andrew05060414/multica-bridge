# multica-bridge

Shared GitHub reusable workflows for Multica relays.

The repository contains only GitHub Actions workflow definitions. A product
repository keeps the event trigger and calls a versioned workflow from this
repository. The first workflow relays closed pull requests to Multica.

## Available workflows

### `pr-closed-relay.yml`

The caller owns the event trigger:

```yaml
name: Multica PR closed relay

on:
  pull_request:
    types: [closed]

permissions: {}

jobs:
  relay:
    uses: andrew05060414/multica-bridge/.github/workflows/pr-closed-relay.yml@v1
    secrets: inherit
```

The calling repository must define the `MULTICA_PR_CLOSED_WEBHOOK_URL`
repository secret. The reusable workflow does not store or print the secret.
It sends a bounded payload with the `pr_closed` action, repository name, pull
request number and state, merge information, branch refs, URL, sender, and a
stable delivery identifier.

The workflow does not check out the calling repository and does not execute
repository code. Its only external operation is the POST to the configured
Multica webhook.

## Secret inheritance

`secrets: inherit` passes caller secrets when the caller and reusable workflow
are in the same GitHub organization or enterprise. The current repositories
are owned by the personal account `andrew05060414`; GitHub's documented scope
for `inherit` must be verified before relying on the cross-repository secret
handoff. If that account layout rejects inheritance, the caller must use an
explicit named secret mapping instead of `secrets: inherit`.

## Versioning

Callers use the major tag `@v1`, never `@main`. Compatible changes move the
`v1` tag after validation. Breaking workflow contracts use a new major tag.
