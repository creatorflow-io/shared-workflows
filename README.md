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
  - PushToNuGet: `true` (default) pushes with `NUGET_ORG_KEY`; `false` leaves the packages (`.nupkg`, `.snupkg`) as the
    `nupkg` artifact for the caller to push, for trusted publishing
- Secrets:
  - NUGET_ORG_KEY: long-lived API key, required when PushToNuGet is true
  - GIT_HUB_PAT (required)

#### Trusted publishing
nuget.org checks the policy's workflow file against the workflow that runs the login, and its repository against the
calling repository, so the login and push cannot run inside this shared workflow
([NuGetGallery#11000](https://github.com/NuGet/NuGetGallery/issues/11000)). Push from the caller instead:

1. On nuget.org, **Trusted Publishing**, add a GitHub Actions policy: the repository owner, the repository, and the
   caller's workflow file name (e.g. `nuget-release.yml`). Leave the environment empty.
2. Add the secret `NUGET_USER`: the nuget.org profile name of the user who created the policy (not an organization,
   not an email).
3. Call this workflow with `PushToNuGet: false` and push in a job of your own:

```
jobs:
    build:
      uses: creatorflow-io/shared-workflows/.github/workflows/shared-publish.yml@net8
      secrets: inherit
      with:
        PushToNuGet: false
    publish:
      needs: build
      runs-on: ubuntu-latest
      permissions:
        id-token: write
      steps:
      - uses: actions/download-artifact@v4
        with:
          name: nupkg
          path: nupkg
      - uses: NuGet/login@v1
        id: login
        with:
          user: ${{ secrets.NUGET_USER }}
      - run: dotnet nuget push "nupkg/*.nupkg" --source https://api.nuget.org/v3/index.json --api-key ${{ steps.login.outputs.NUGET_API_KEY }} --skip-duplicate
```

4. Remove `NUGET_ORG_KEY` once a release has published through the policy.
