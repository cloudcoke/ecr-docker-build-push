# Docker Build and ECR Push Actions

# Usage

```yaml
- uses: cloudcoke/ecr-docker-duild-push@v2.1
  with:
    # AWS 인증 정보 [필수]
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

    # 사용할 AWS 리전 (기본값: ap-northeast-2)
    aws-region: ap-northeast-2

    # ECR 레포지토리 이름 [필수]
    repository: my-app

    # 도커 이미지 태그 (기본값: latest)
    image-tag: latest

    # 빌드 인자 (환경 변수 등)
    build-args: |
        NODE_ENV=production
        API_URL=https://api.example.com

    # 추가 도커 빌드 컨텍스트
    build-contexts: app1=app1/src,app2=app2/src

    # 외부 캐시 소스 (기본값: type=gha)
    cache-from: type=gha

    # 캐시 내보내기 설정 (기본값: type=gha,mode=max)
    cache-to: type=gha,mode=max

    # 빌드 컨텍스트 경로 (기본값: .)
    context: .

    # Dockerfile 경로 (기본값: ./Dockerfile)
    file: ./Dockerfile

    # 빌드 완료 후 이미지 푸시 여부 (기본값: true)
    push: true

    # 타겟 플랫폼 (기본값: linux/amd64)
    platforms: linux/amd64,linux/arm64

    # 빌드에 대한 출처 증명 생성 여부 (기본값: false)
    # 고급 사용 예시 (mode=max) (mode=min)
    provenance: true

    # Docker BuildKit secrets
    secrets: |
        id=mysecret,src=path/to/local/secret.txt

    # BuildKit 환경 변수로 전달할 비밀 값들
    secret-envs: |
        NPM_TOKEN
        SENTRY_AUTH_TOKEN

    #파일로 전달할 비밀 값들
    secret-files: |
        npmrc=./npmrm
        ssh_key=./id_rsa
```

# 예시

## 기본 예시

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
              uses: cloudcoke/ecr-docker-build-push@v2.1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
```

## 멀티 플랫폼 빌드

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
              uses: cloudcoke/ecr-docker-build-push@v2.1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
                  build-platforms: linux/amd64,linux/arm64
```

## Dockerfile 지정

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
              uses: cloudcoke/ecr-docker-build-push@v2.1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}
                  build-file: path/to/your/Dockerfile
```

# ECR에 이미지를 Push하기 위해 필요한 IAM 권한

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ECRGEtAuthToken",
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken"
            ],
            "Resource": "*"
        },
        {
            "Sid": "ECRPush",
            "Effect": "Allow",
            "Action": [
                "ecr:UploadLayerPart",
                "ecr:PutImage",
                "ecr:InitiateLayerUpload",
                "ecr:CompleteLayerUpload",
                "ecr:BatchGetImage",
                "ecr:BatchCheckLayerAvailability"
            ],
            "Resource": "arn:aws:ecr:ap-northeast-2:123456789012:repository/myproject-*-accounts"
        }
    ]
}
```
 