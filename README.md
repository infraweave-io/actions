# Github Actions 🚀

This repository contains ready-to-use actions for your pipelines.

## Examples ✨

Below are some example on how they can be used:

### How to test a module 🧪

This example expects the following structure:

```
.
├── .github/
│   └── workflows/
│       └── test.yaml
├── src
│   ├── main.tf
│   ├── module.yaml
│   ├── outputs.tf
│   └── variables.tf
└── tests
    ├── test1-something.py
    ├── test2-othercheck.py
    └── test3-thirdcheck.py
```

This will:
* lint the terraform code
* if it passes; find all python files in the `tests` folder and run them in parallel

```yaml
# Filename: ./github/workflows/test.yaml

name: Test Module

on: push

permissions:
  id-token: write # for OIDC
  contents: read

jobs:
  terraform-lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3
      - name: Set Up Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: '1.5.7'
      - name: Install TFLint
        run: |
          curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash
      - name: Run TFLint
        run: tflint --chdir "./src"

  run-tests:
    needs: terraform-lint
    uses: infraweave-io/actions/.github/workflows/test-module.yaml@82e5da795aa69fac859a44afeb569eb561ec7eff # v0.0.70
    with:
      central_account_id: "000000000000"
      workload_account_id: "111111111111"
      environment: "prod"
      identifier: ${{ github.sha }}

```

### How to publish a module 📦

This example expects the following structure:

```
.
├── .github/
│   └── workflows/
│       └── publish-module.yaml
├── src
│   ├── main.tf
│   ├── module.yaml
│   ├── outputs.tf
│   └── variables.tf
...
```

This will:
* publish an InfraWeave module with the desired version

```yaml
# Filename: ./github/workflows/publish-module.yaml

name: Publish Module

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to publish'
        required: true
        type: string
        default: 0.0.1-dev+some-branch-replace-me

permissions:
  id-token: write # for OIDC
  contents: read

jobs:
  publish-module-dev:
    uses: infraweave-io/actions/.github/workflows/publish-module.yaml@82e5da795aa69fac859a44afeb569eb561ec7eff # v0.0.70
    with:
      central_account_id: "000000000000"
      environment: "prod"
      version: ${{ inputs.version }}
```

### How to publish a stack 🏗️

This example expects the following structure:

```
.
├── .github/
│   └── workflows/
│       └── publish-stack.yaml
├── src
│   ├── claim-bucket.yaml
│   ├── claim-ec2.yaml
│   ├── claim-s3.yaml
│   └── stack.yaml
...
```

This will:
* publish an InfraWeave stack with the desired version

```yaml
# Filename: ./github/workflows/publish-stack.yaml

name: Publish Stack

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to publish'
        required: true
        type: string
        default: 0.0.1-dev+some-branch-replace-me

permissions:
  id-token: write # for OIDC
  contents: read

jobs:
  publish-module-dev:
    uses: infraweave-io/actions/.github/workflows/publish-stack.yaml@82e5da795aa69fac859a44afeb569eb561ec7eff # v0.0.70
    with:
      central_account_id: "000000000000"
      environment: "prod"
      version: ${{ inputs.version }}

```