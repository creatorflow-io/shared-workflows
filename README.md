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
  - JuiceRefVersion: Juice's packages version (6.x.x)
  
```
jobs:
    build:
      uses: creatorflow-io/shared-workflows/.github/workflows/shared-publish.yml@master
      with:
        JuiceRefVersion: 6.0.4
      secrets: inherit
```
