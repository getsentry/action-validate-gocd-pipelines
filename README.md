# action-validate-gocd-pipelines

Validates GoCD pipelines.

To use this in a new repository you'll need to add it to `gha-gocd-api` in security-as-code.
Open a PR like [this](https://github.com/getsentry/security-as-code/pull/286) one.


## Example Usage

```yaml
jobs:
    files-changed:
        name: files-changed
        runs-on: ubuntu-latest
        # Map a step output to a job output
        outputs:
            gocd: ${{ steps.changes.outputs.gocd }}
        steps:
          - name: Check for relevant file changes
            uses: getsentry/paths-filter@4512585405083f25c027a35db413c2b3b9006d50 # v2.11.1
            id: changes
            with:
              filters: |
                gocd:
                  - 'gocd/**'

    validate:
        if: needs.files-changed.outputs.gocd == 'true'
        needs: files-changed
        name: Validate GoCD Pipelines
        runs-on: ubuntu-latest

        # required for google auth
        permissions:
            contents: "read"
            id-token: "write"

        steps:
            - uses: actions/checkout@v3
            - id: 'auth'
              uses: google-github-actions/auth@v1
              with:
                workload_identity_provider: 'projects/868781662168/locations/global/workloadIdentityPools/prod-github/providers/github-oidc-pool'
                service_account: 'gha-gocd-api@sac-prod-sa.iam.gserviceaccount.com'
                token_format: 'id_token'
                id_token_audience: '610575311308-9bsjtgqg4jm01mt058rncpopujgk3627.apps.googleusercontent.com'
                id_token_include_email: true
            - uses: getsentry/action-validate-gocd-pipelines@v1
              with:
                configrepo: chartcuterie__master
                gocd_access_token: ${{ secrets.GOCD_ACCESS_TOKEN }}
                google_oidc_token: ${{ steps.auth.outputs.id_token }}
```
