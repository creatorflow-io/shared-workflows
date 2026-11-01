# shared-build

Define reusable build/pack workflows.



### shared-build.yml
Run build and unit test.

- Inputs:
  - Stage: alpha, beta... (required)
  - JuiceRefVersion: Juice's packages version (6.x.x)

```
jobs:
  build:
    uses: creatorflow-io/shared-workflows/.github/workflows/shared-build.yml@master
    with:
       Stage: alpha
       JuiceRefVersion: 6.0.0
    secrets: inherit
```


### shared-pack.yml
Run build, pack and push nuget packages to private Github nuget registry.
Packages versioning by `github.run_number`.

- Inputs:
  - Stage: alpha, beta... (required)
  - JuiceRefVersion: Juice's packages version (6.x.x)

```
jobs:
  build:
    uses: creatorflow-io/shared-workflows/.github/workflows/shared-pack.yml@master
    with:
       Stage: alpha
       JuiceRefVersion: 6.0.0
    secrets: inherit
```


### shared-publish.yml
Run build, pack and push release to Nuget.org registry.
Packages versioning by github release tags.

- Inputs:
  - Target: .NET SDK versions to set up
- Secrets:
  - NUGET_USER: nuget.org profile name for [trusted publishing](https://learn.microsoft.com/nuget/nuget-org/trusted-publishing) (recommended)
  - NUGET_ORG_KEY: long-lived API key, used only when NUGET_USER is not set
  - GIT_HUB_PAT (required)

#### Trusted publishing
1. On nuget.org, **Trusted Publishing**, add a GitHub Actions policy: the repository owner, the repository, and the
   workflow file of the **calling** workflow (file name only, e.g. `nuget-release.yml`). Leave the environment empty.
2. Add the repository secret `NUGET_USER` (your nuget.org profile name, not your email).
3. Grant `id-token: write` on the calling job; a reusable workflow cannot get more permissions than its caller.
4. Remove `NUGET_ORG_KEY` once a release has published through the policy.

```
jobs:
    build:
      permissions:
        contents: read
        id-token: write
      uses: creatorflow-io/shared-workflows/.github/workflows/shared-publish.yml@net8
      secrets: inherit
      with:
        Target: |
          8.0.x
          10.0.x
```
