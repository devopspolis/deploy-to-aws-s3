# 🚀 Deploy directory to AWS S3

This GitHub Action uploads a directory to an AWS S3 bucket. It optionally runs a script to build or prepare the directory before uploading, and supports tagging the destination bucket.

---

## 📥 Inputs

| Name                | Description                                                                 | Required | Default                          |
|---------------------|-----------------------------------------------------------------------------|-------|----------------------------------|
| `directory`         | Path to the local directory to upload to S3                                 | true  | —                                |
| `bucket`            | Name of the S3 bucket to upload to                                          | true  | —                                |
| `bucket_region`     | AWS region where the S3 bucket resides                                      | true  | —                                |
| `delete`            | Whether to delete files in S3 not found in the local directory              | false | `true`                           |
| `ref`               | Git branch, tag, or SHA to checkout                                         | false | `default branch`        |
| `script`            | Optional script command to run before uploading (e.g., `build.sh --prod`)   | false | `""`                             |
| `working-directory` | Directory in which to run the script                                        | false | `.`                              |
| `tags`              | Comma-separated `key=value` pairs to tag the bucket                         | false | `""`                             |

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
      - name: Deploy directory to AWS S3
        uses: devopspolis/deploy-to-aws-s3@main
        with:
          directory: dist
          bucket: my-app-bucket
          bucket_region: us-west-2
          script: build.sh --prod
          tags: Version=1.2.3,Environment=production
