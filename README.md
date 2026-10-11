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

### `optimize`

**Required** The bundle build optimization level [ `disabled` | `recommended` | `aggressive`] used.

Default: `disabled`

[Documentation](https://www.openpolicyagent.org/docs/policy-performance#optimization-levels)

### `entrypoint`

Slash separated entrypoint path

Default: `` (empty string) 

**Required** when used in combination with --optimize=[`recommended` | `aggressive`]

### `rego_version`

The rego version used to build the policy image [ `rego.v0` | `rego.v1` ].

Default: `rego.v1`

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

    - 
      uses: actions/checkout@v7
    - 
      name: Policy Login
      id: policy-login
      uses: opcr-io/policy-login-action@v7
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        username: policy-bot
        password: "$GITHUB_TOKEN"
    - 
      name: Policy Build
      id: policy-build
      uses: opcr-io/policy-build-action@v4
      with:
        src: peoplefinder/src
        tag: demo/peoplefinder:$(svu patch)
        revision: "$GITHUB_SHA"
        optimize: aggressive
    - 
      name: Policy Push
      id: policy-push
      uses: opcr-io/policy-push-action@v4
      with:
        tag: demo/peoplefinder:$(svu patch)
    - 
      name: Policy Logout
      id: policy-logout
      uses: opcr-io/policy-logout-action@v4

```

> NOTE: The above example leverages the `svu` (semantic version utility) from https://github.com/caarlos0/svu