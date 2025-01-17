# Policy Build Action

## policy-build-action

GitHub action for building policy images


## Inputs

### `src`

**Required** The directory path to the input source to build.

Default: `src`

### `tag`

**Required** The build tag applied to created policy image.

Default: empty

### `revision`

Policy bundle revision

Default: "${GITHUB_SHA}"

### `verbosity`

**Required** The logging verbosity level [ `info` | `error` | `debug` | `trace` ] used.

Default: `error`

## `rego_version`

The rego version used to build the policy image.
Default: `rego.v0`

## Outputs

None defined


## Example

```
name: policy-build-release

on:
  workflow_dispatch:
  push:
    tags:
    - '*'

jobs:
  release_policy:
    runs-on: ubuntu-latest
    name: build
    steps:

    - uses: actions/checkout@v2

    - name: Policy Login
      id: policy-login
      uses: opcr-io/policy-login-action@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        username: policy-bot
        password: "$GITHUB_TOKEN"

    - name: Policy Build
      id: policy-build
      uses: opcr-io/policy-build-action@v2
      with:
        src: peoplefinder/src
        tag: datadude/peoplefinder:$(sver -n patch)
        revision: "$GITHUB_SHA"

    - name: Policy Push
      id: policy-push
      uses: opcr-io/policy-push-action@v2
      with:
        tag: datadude/peoplefinder:$(sver -n patch)

    - name: Policy Logout
      id: policy-logout
      uses: opcr-io/policy-logout-action@v2

```
