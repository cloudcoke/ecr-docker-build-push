# Docker Build and ECR Push Actions

# Usage

```yaml
- uses: cloudcoke/ecr-docker-duild-push@v1
  with:
      # AWS IAM access key to use
      aws-access-key-id: ""

      # AWS IAM access secret key to use
      aws-secret-access-key: ""

      # Which AWS region to use
      # Default: ap-northeast-2
      aws-region: ""

      # Name of ECR repository to use
      repository: ""

      # Docker image tag to use
      # Default: latest
      image-tag: ""

      # List of target platforms for build
      # Default: linux/amd64
      # For example: linux/amd64,linux/arm64
      build-platforms: ""

      # Path to docker build context
      # Default: .
      build-context: ""

      # Path to the Dockerfile
      # Default: ./Dockerfile
      # For example: path/to/your/Dockerfile
      build-file: ""
```

# Examples

## Default build and push

```yaml
name: Build and Push

on:
    push:
        branches: main

jobs:
    build-push:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Code
              uses: actions/checkout@v4

            - name: Docker build and ECR push
              uses: cloudcoke/ecr-docker-build-push@v1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
```

## Build multiple platforms

```yaml
name: Build and Push

on:
    push:
        branches: main

jobs:
    build-push:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Code
              uses: actions/checkout@v4

            - name: Docker build and ECR push
              uses: cloudcoke/ecr-docker-build-push@v1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
                  build-platforms: linux/amd64,linux/arm64
```

## Build your Dockerfile

```yaml
name: Build and Push

on:
    push:
        branches: main

jobs:
    build-push:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Code
              uses: actions/checkout@v4

            - name: Docker build and ECR push
              uses: cloudcoke/ecr-docker-build-push@v1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
                  build-file: path/to/your/Dockerfile
```

# Required IAM Permissions

```json
{
    "Statement": [
        {
            "Action": [
                "ecr:UploadLayerPart",
                "ecr:PutImage",
                "ecr:InitiateLayerUpload",
                "ecr:GetAuthorizationToken",
                "ecr:CompleteLayerUpload",
                "ecr:BatchGetImage",
                "ecr:BatchCheckLayerAvailability"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ],
    "Version": "2012-10-17"
}
```
