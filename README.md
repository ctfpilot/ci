# CTF Pilot's CI

> [!IMPORTANT]
> This CI repository is meant for internal usage within the CTFPilot organization.

This repository contains Continuous intergration automation, in the forms of GitHub Action workflows.  

## How to use

To use the reusable workflows, use the following template to use:

```yml
name: <name>

on:
  <triggers>

jobs:
  <job>:
    permissions:
      <permissions>
    name: <name>
    uses: ctfpilot/ci/.github/workflows/<workflow>.yml@<version>
    with:
      <inputs>
```

## Workflows

- [`cla-assistant`](#cla-assistant): CLA Assistant bot
- [`release`](#release): Release system
- [`docker`](#docker): Docker build and push system
- [`develop-update`](./develop-update/README.md): Update develop branch to match main branch

### CLA Assistant

This workflow contains the CLA Assistant bot used throughout CTF Pilot.

It is setup to contain sensible defaults, but requires the `repository` input to be specified.

The workflow can only be run within the `ctfpilot` organization.

#### Inputs

- `repository`: The repository that the CLA is generated for. Example `hello-world`.
- `version`: CLA version. Keep default to use organization default.
- `CLASHA`: The SHA for the commit, the CLA version is. Keep default to use organization default.

#### Secrets

- `CLA_ASSISTANT_PAT`: CLA Repository Access Token. Required in order to push signatures to the CLA repository.

#### How to use

```yml
name: "CLA Assistant"

on:
  issue_comment:
    types: [created]
  pull_request_target:
    types: [opened, closed, synchronize]

jobs:
  CLAAssistant:
    permissions:
      actions: write
      contents: read
      pull-requests: write
      statuses: write
    name: "CLA Assistant"
    uses: ctfpilot/ci/.github/workflows/cla-assistant.yml@<version>
    secrets: inherit
    with:
      repository: <repository>
```

### Release

This workflow contains the release system used throughout CTF Pilot.

The workflow requires the `repository` input to be specified.

#### Inputs

- `repository`: Allowed repository for workflow to run in. Example `ctfpilot/hello-world`.
- `ENVIRONMENT`: The environment to deploy to.

#### Secrets

- `RELEASE_GH_TOKEN`: GitHub Token. Used to authenticate with GitHub at release step. This will overwrite the use of the default GitHub token.
- `BUILD_GH_TOKEN`: GitHub Token. Used to authenticate with GitHub at build step. This will overwrite the use of the default GitHub token.

#### Outputs

- `version`: The version of the release. Will be null if no release was made.

#### How to use

```yml
name: "Release"

on:
  push:
    branches:
      - main
      - develop

jobs:
  CLAAssistant:
    permissions:
      contents: write
      packages: write
      id-token: write
    name: "Release"
    uses: ctfpilot/ci/.github/workflows/release.yml@<version>
    with:
      repository: <repository>
```

### Docker

This workflow contains the Docker build and push system used throughout CTF Pilot.

The workflow requires the `repository` input to be specified.

#### Inputs

- `repository`: Allowed repository for workflow to run in. Example `ctfpilot/hello-world`.
- `dockerfile`: Dockerfile path.
- `context`: Build context.
- `arguments`: Build arguments. List of key-value pairs.
- `semver`: Semantic version. Leave empty to not use semantic versioning.
- `tags`: List of tags to apply to the image. Required if you do not use semantic versioning.
- `registry`: Registry for docker image to use. Defaults to GitHub container registry.
- `image_name`: Docker image name to use. Defaults to repository name.
- `registry_username`: Username to use for registry login. Defaults to GitHub actor.
- `registry_token`: Token to use for registry login. Defaults to GITHUB_TOKEN.
- `fetch_submodules`: Fetch submodules. Defaults to true.
- `platforms`: Platforms to build for (comma separated, e.g., linux/amd64,linux/arm64).
- `runner`: Runner to use for the job. Defaults to ubuntu-latest
- `cacheFrom`: Cache type from
- `cacheTo`: Cache type to
- `commit`: Commit SHA to use for git operations and tagging. Defaults to github.sha.

#### How to use

```yml
name: "Docker build and push"

on:
  push:
    branches:
      - main
      - develop

jobs:
  CLAAssistant:
    permissions:
      contents: read
      packages: write
      id-token: write
    name: "Docker build and push"
    uses: ctfpilot/ci/.github/workflows/docker.yml@<version>
    with:
      repository: <repository>
```

### Develop Update

This workflow updates the `develop` branch to match the latest version of the `main` branch.

The workflow requires the `repository` input to be specified.

The workflow intelligently handles different merge scenarios:

- **Normal develop flow**: When the latest commit on `main` was merged from a `develop` PR, the workflow will auto-merge (if enabled) to keep develop synchronized.
- **Hotfix detection**: When the latest commit on `main` was merged from a different branch (e.g., a hotfix), the workflow will:
  - Skip auto-merge to allow manual review
  - Add a comment to the PR explaining the situation
  - Create a workflow warning for visibility

This ensures that hotfixes and other direct merges to `main` are properly reviewed before being merged back to `develop`.

If a merge is not detected, the main and develop branches are already in sync or an existing PR between main and develop exists, the workflow will exit without merging changes, but will create a PR if possible.

#### Inputs

- `repository`: Allowed repository for workflow to run in. Example `ctfpilot/hello-world`.
- `auto_merge`: Whether to automatically merge the PR after creating it. Defaults to true. Note: Auto-merge will be skipped if the latest commit on `main` was not from a `develop` branch PR.
- `pr_description`: Additional description to add to the PR body.

#### How to use

```yml
name: "Update Develop Branch"

on:
  push:
    branches:
      - main

jobs:
  CLAAssistant:
    permissions:
      contents: read
      pull-requests: write
      issues: write
    name: "Update Develop Branch"
    uses: ctfpilot/ci/.github/workflows/develop-update.yml@<version>
    with:
      repository: <repository>
```

## Contributing

We welcome contributions of all kinds, from **code** and **documentation** to **bug reports** and **feedback**!

Please check the [Contribution Guidelines (`CONTRIBUTING.md`)](/CONTRIBUTING.md) for detailed guidelines on how to contribute.

To maintain the ability to distribute contributions across all our licensing models, **all code contributions require signing a Contributor License Agreement (CLA)**.
You can review **[the CLA here](https://github.com/ctfpilot/cla)**. CLA signing happens automatically when you create your first pull request.  
To administrate the CLA signing process, we are using **[CLA assistant lite](https://github.com/marketplace/actions/cla-assistant-lite)**.

*A copy of the CLA document is also included in this repository as [`CLA.md`](CLA.md).*  
*Signatures are stored in the [`cla` repository](https://github.com/ctfpilot/cla).*

## License

This schema and repository is licensed under the **EUPL-1.2 License**.  
You can find the full license in the **[LICENSE](LICENSE)** file.

We encourage all modifications and contributions to be shared back with the community, for example through pull requests to this repository.  
We also encourage all derivative works to be publicly available under the **EUPL-1.2 License**.  
At all times must the license terms be followed.

For information regarding how to contribute, see the [contributing](#contributing) section above.

CTF Pilot is owned and maintained by **[The0Mikkel](https://github.com/The0mikkel)**.  
Required Notice: Copyright Mikkel Albrechtsen (<https://themikkel.dk>)

## Code of Conduct

We expect all contributors to adhere to our [Code of Conduct](/CODE_OF_CONDUCT.md) to ensure a welcoming and inclusive environment for all.
