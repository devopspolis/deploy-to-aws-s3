# 🚀 Deploy to AWS S3 GitHub Action

This GitHub Action uploads a directory to an AWS S3 bucket. It optionally runs a script to build or prepare the directory before deployment, and supports tagging the destination bucket.

---

## 📥 Inputs

| Name                | Description                                                                 | Required | Default                          |
|---------------------|-----------------------------------------------------------------------------|----------|----------------------------------|
| `directory`         | Path to the local directory to upload to S3                                 | ✅       | —                                |
| `bucket`            | Name of the S3 bucket to upload to                                          | ✅       | —                                |
| `bucket_region`     | AWS region where the S3 bucket resides                                      | ✅       | —                                |
| `delete`            | Whether to delete files in S3 not found in the local directory              | ❌       | `true`                           |
| `ref`               | Git branch, tag, or SHA to checkout                                         | ❌       | `${{ github.ref_name }}`        |
| `script`            | Optional script command to run before uploading (e.g., `build.sh --prod`)   | ❌       | `""`                             |
| `working-directory` | Directory to run the script in                                               | ❌       | `.`                              |
| `tags`              | Comma-separated `key=value` pairs to tag the bucket                         | ❌       | `""`                             |

---

## 📤 Outputs

| Name         | Description                       |
|--------------|-----------------------------------|
| `bucket_arn` | The ARN of the deployed S3 bucket |

---

## 📦 Usage

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to S3
        uses: devopspolis/deploy-to-aws-s3-action@v1
        with:
          directory: dist
          bucket: my-app-bucket
          bucket_region: us-west-2
          script: build.sh --prod
          tags: Version=1.2.3,Environment=production
