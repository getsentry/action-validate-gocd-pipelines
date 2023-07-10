# action-validate-gocd-pipelines

Validates GoCD pipelines.


## Example Usage

ops will need to authorize access to your repo for `gha-gocd-api` in security-as-code - open an ops request for this.

```yaml
jobs:
    validate:
        name: Validate GoCD Pipelines
        runs-on: ubuntu-latest

        # required for google auth
        permissions:
            contents: "read"
            id-token: "write"

        steps:
            - id: 'auth'
              uses: google-github-actions/auth@v1
              with:
                workload_identity_provider: 'projects/868781662168/locations/global/workloadIdentityPools/prod-github/providers/github-oidc-pool'
                service_account: 'gha-gocd-api@sac-prod-sa.iam.gserviceaccount.com'
                token_format: 'access_token'
            - uses: actions/checkout@v3
            - uses: getsentry/action-validate-gocd-pipelines@v1
              with:
                configrepo: getsentry__master
                gocd_access_token: ${{ secrets.GOCD_ACCESS_TOKEN }}
                google_oidc_token: ${{ steps.auth.outputs.access_token }}
```
