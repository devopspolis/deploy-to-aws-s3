<div style="display: flex; align-items: center;">
  <img src="logo.png" alt="Logo" width="50" height="50" style="margin-right: 10px;"/>
  <span style="font-size: 2.2em;">Deploy to AWS S3</span>
</div>

<p>
This GitHub Action uploads a directory to an AWS S3 bucket. It optionally runs a script to build or prepare the directory and supports tagging the destination bucket.
</p>

![GitHub Marketplace](https://img.shields.io/badge/GitHub%20Marketplace-Deploy%20to%20AWS%20S3-blue?logo=github)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

See more [GitHub Actions by DevOpspolis](https://github.com/marketplace?query=devopspolis&type=actions)

---

## 📚 Table of Contents
- [✨ Features](#features)
- [📥 Inputs](#inputs)
- [📤 Outputs](#outputs)
- [📦 Usage](#usage)
- [🚦 Requirements](#requirements)
- [🧑‍⚖️ Legal](#legal)
---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="features"></a>
## ✨ Features
- Deploys directory to AWS S3
---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="inputs"></a>
## 📥 Inputs

| Name                | Description                                              | Required | Default          |
| ------------------- | -------------------------------------------------------- | -------- | ---------------- |
| `directory`         | The directory (source)                                   | true     | —                |
| `bucket`            | The S3 bucket (destination)                              | true     | —                |
| `bucket_region`     | S3 bucket region                                         | true     | —                |
| `delete`            | Delete files in destination not found in the source      | false    | `true`           |
| `script`            | Script to run before uploading (e.g., `build.sh --prod`) | false    | none             |
| `working-directory` | Directory from which to run the script                   | false    | `.`              |
| `tags`              | bucket tags (e.g. version=v1.2.0,environment=dev)        | false    | none             |
| `role`              | IAM role ARN or name to assume for deployment            | false    | —                |

---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="outputs"></a>
## 📤 Outputs

| Name             | Description                                   |
| ---------------- | --------------------------------------------- |
| `bucket_arn`     | The ARN of the deployed S3 bucket             |
| `integrity_hash` | The MD5 integrity hash of the bucket contents |

---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="usage"></a>
## 📦 Usage

### Syntax
```yaml
uses: devopspolis/deploy-to-aws-s3@<version>
with:
  [inputs]
```

Example 1 - Uploads existing repository directory `docs` to S3 bucket `my-bucket-name`

Note: Files in the destination S3 bucket not found in the source directory will be deleted by default. Set `delete: false` to disable.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy directory to AWS S3
        uses: devopspolis/deploy-to-aws-s3@main
        with:
          directory: docs
          bucket: my-bucket-name
          bucket_region: us-west-2
        env:
          AWS_ACCOUNT_ID: ${{ vars.AWS_ACCOUNT_ID }}
          AWS_REGION: ${{ vars.AWS_REGION }}
```

Example 2 - Runs `build.sh` script to create and upload `reports` directory. Adds bucket tags.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy directory to AWS S3
        uses: devopspolis/deploy-to-aws-s3@main
        with:
          directory: reports
          bucket: my-app-bucket
          bucket_region: us-west-2
          script: build.sh
          tags: version=1.2.3,environment=production
        env:
          AWS_ACCOUNT_ID: ${{ vars.AWS_ACCOUNT_ID }}
          AWS_REGION: ${{ vars.AWS_REGION }}
```

Example 3 - Runs `build.sh` script located in `scripts` directory with argument `--prod` to create and upload `dist` directory.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy directory to AWS S3
        uses: devopspolis/deploy-to-aws-s3@main
        with:
          directory: dist
          bucket: my-app-bucket
          bucket_region: us-west-2
          script: "build.sh --prod"
          working-directory: scripts
        env:
          AWS_ACCOUNT_ID: ${{ vars.AWS_ACCOUNT_ID }}
          AWS_REGION: ${{ vars.AWS_REGION }}
```
---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="requirements"></a>
## 🚦Requirements

1. The calling workflow must have the permissions shown below.
1. The calling workflow must provide access and permission to upload to the AWS S3 bucket. Best practice is to set up OIDC authentication between the GitHub repository and AWS account, and then assume a role with the necessary permissions to access and putObject to the bucket.

   In the example below the `AWS_ACCOUNT_ID` and `AWS_REGION` are retrieved from the GitHub repository environment variables, enabling the workflow to target environment specific AWS accounts.

```yaml
permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Set up AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::${{ vars.AWS_ACCOUNT_ID }}:role/deploy-to-aws-s3-role
        env:
          AWS_ACCOUNT_ID: ${{ vars.AWS_ACCOUNT_ID }}
          AWS_REGION: ${{ vars.AWS_REGION }}
```
---
<!-- trunk-ignore(markdownlint/MD033) -->
<a id="legal"></a>
## 🧑‍⚖️ Legal
The MIT License (MIT)
