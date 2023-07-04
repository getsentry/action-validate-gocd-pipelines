# action-validate-gocd-pipelines

Validates GoCD pipelines.


## Example Usage

ops will need to add your repo to `github_actions_allowed_repos` - open an ops request for this.

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
            - uses: actions/checkout@ac593985615ec2ede58e132d2e21d2b1cbd6127c  # v3
            - uses: ./.github/actions/validate-gocd-pipelines
              with:
                configrepo: getsentry__master
                gocd_access_token: ${{ secrets.GOCD_ACCESS_TOKEN }}
```
