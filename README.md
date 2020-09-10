# amplify-preview-actions

[![RELEASE](https://img.shields.io/github/v/release/yinlinchen/amplify-preview-actions?include_prereleases)](https://github.com/ambientlight/amplify-cli-action/releases)
[![LICENSE](https://img.shields.io/github/license/yinlinchen/amplify-preview-actions)](https://github.com/yinlinchen/amplify-preview-actions/blob/master/LICENSE)
[![ISSUES](https://img.shields.io/github/issues/yinlinchen/amplify-preview-actions)](https://github.com/yinlinchen/amplify-preview-actions/issues)

AWS Amplify pull request preview currently only supports private github repositoy. This action deploys your AWS Amplify pull request preview for your public repository.

## Getting Started
You can include the action in your workflow as `actions/amplify-preview-actions@0.1`.

* Example: Deploy a PR preview after a reviewer is assigned.

```yaml
name: 'Amplify PR Preview'
on:
  pull_request:
    types: [review_requested]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master

    - name: set branchname env
      id: setenvname
      run: |
        # use GITHUB_HEAD_REF that is set to PR source branch
        # also remove -_ from branch name and limit length to 10 for amplify env restriction
        echo "##[set-output name=setbranchname;]$(echo ${GITHUB_HEAD_REF//[-_]/} | cut -c-10)"

    - name: deploy PR preview
      uses: yinlinchen/amplify-preview-actions@0.2
      with:
        branch_name: ${{ steps.setenvname.outputs.setbranchname }}
        amplify_command: deploy
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AmplifyAppId: ${{ secrets.AmplifyAppId }}
        BackendEnvARN: ${{ secrets.BackendEnvARN }}
        AWS_REGION: 'us-east-1'
```

* Advanced configuration: Deploy a PR preview after a reviewer is assigned and the PR is labeled with ```Ready for review```

```
jobs:
  deploy:
    if: contains(github.event.pull_request.labels.*.name, 'Ready for review')
    runs-on: ubuntu-latest
```