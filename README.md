# CTF Pilot's CI

> [!IMPORTANT]
> This CI repository is meant for internal usage within the CTFPilot organization.

This repository contains Continuous intergration automation, in the forms of Github Action workflows.  

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
    uses: ctfpilot/ci/.github/workflows/<workflow>@<version>
    with:
      <inputs>
```

## Workflows

- [`cla-assistant`](#cla-assistant): CLA Assistant bot, with input values for customizable values.

### CLA Assistant

This workflow contains the CLA Assistant bot used througout CTF Pilot.

It is setup to contain sensible defaults, but requires the `repository` input to be specified.

#### Inputs

- `repository`: The repository that the CLA is generated for
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
    uses: ctfpilot/ci/.github/workflows/cla-assistant@<version>
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
