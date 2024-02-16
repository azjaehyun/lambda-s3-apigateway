
# 사전 준비
### sam install
```
https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html
git clone https://github.com/aws-samples/amazon-s3-presigned-urls-aws-sam
cd amazon-s3-presigned-urls-aws-sam
sam deploy --guided << 명령어 때리기 전에 samconfig.toml 수정
```

### sam 이란? 쌈밥 아니다~~
```
AWS SAM CLI는 AWS SAM 템플릿과 지원되는 타사 통합과 함께 사용하여 서버리스 애플리케이션을 빌드하고 실행할 수 있는 명령줄 도구입니다.
```



### samconfig.toml 수정 예시 아래
```
version = 0.1
[default.deploy.parameters]
stack_name = "s3uploader-lambda"  << 본인이 생성하고 싶은 이름으로 수정 (s3_prefix과 이름 같게)
resolve_s3 = true
s3_prefix = "s3uploader-lambda" << 본인이 생성하고 싶은 이름으로 수정 (stack_name과 이름 같게)
region = "us-west-2"
confirm_changeset = true
capabilities = "CAPABILITY_IAM"
disable_rollback = true
image_repositories = []
```


### sam 명령어를 통해서 deploy 하자! ( 이걸 하면 apigateway + lambda + s3 가 생성된다! )
```
➜  amazon-s3-presigned-urls-aws-sam git:(master) sam deploy

		Managed S3 bucket: aws-sam-cli-managed-default-samclisourcebucket-qdfr4zbm3ppl
		A different default S3 bucket can be set in samconfig.toml
		Or by specifying --s3-bucket explicitly.
File with same data already exists at s3uploader-lambda/18f7c26392ca430998595d4c573aeb76, skipping upload

	Deploying with following values
	===============================
	Stack name                   : s3uploader-lambda
	Region                       : us-west-2
	Confirm changeset            : True
	Disable rollback             : True
	Deployment s3 bucket         : aws-sam-cli-managed-default-samclisourcebucket-qdfr4zbm3ppl
	Capabilities                 : ["CAPABILITY_IAM"]
	Parameter overrides          : {}
	Signing Profiles             : {}

Initiating deployment
=====================

File with same data already exists at s3uploader-lambda/ea65765a86d268ba5a711676bf4a9c4a.template, skipping upload


Waiting for changeset to be created..

CloudFormation stack changeset
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Operation                                                 LogicalResourceId                                         ResourceType                                              Replacement
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+ Add                                                     MyApiApiGatewayDefaultStage                               AWS::ApiGatewayV2::Stage                                  N/A
+ Add                                                     MyApi                                                     AWS::ApiGatewayV2::Api                                    N/A
+ Add                                                     S3UploadBucket                                            AWS::S3::Bucket                                           N/A
+ Add                                                     UploadRequestFunctionRole                                 AWS::IAM::Role                                            N/A
+ Add                                                     UploadRequestFunctionUploadAssetAPIPermission             AWS::Lambda::Permission                                   N/A
+ Add                                                     UploadRequestFunction                                     AWS::Lambda::Function                                     N/A
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Changeset created successfully. arn:aws:cloudformation:us-west-2:767404772322:changeSet/samcli-deploy1708062813/bcde9a81-d629-4a3d-a61c-f7c95752d261


Previewing CloudFormation changeset before deployment
======================================================
Deploy this changeset? [y/N]: y

2024-02-16 14:53:43 - Waiting for stack create/update to complete

CloudFormation events from stack operations (refresh every 5.0 seconds)
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ResourceStatus                                            ResourceType                                              LogicalResourceId                                         ResourceStatusReason
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
CREATE_IN_PROGRESS                                        AWS::CloudFormation::Stack                                s3uploader-lambda                                         User Initiated
CREATE_IN_PROGRESS                                        AWS::S3::Bucket                                           S3UploadBucket                                            -
CREATE_IN_PROGRESS                                        AWS::S3::Bucket                                           S3UploadBucket                                            Resource creation Initiated
CREATE_COMPLETE                                           AWS::S3::Bucket                                           S3UploadBucket                                            -
CREATE_IN_PROGRESS                                        AWS::IAM::Role                                            UploadRequestFunctionRole                                 -
CREATE_IN_PROGRESS                                        AWS::IAM::Role                                            UploadRequestFunctionRole                                 Resource creation Initiated
CREATE_COMPLETE                                           AWS::IAM::Role                                            UploadRequestFunctionRole                                 -
CREATE_IN_PROGRESS                                        AWS::Lambda::Function                                     UploadRequestFunction                                     -
CREATE_IN_PROGRESS                                        AWS::Lambda::Function                                     UploadRequestFunction                                     Resource creation Initiated
CREATE_COMPLETE                                           AWS::Lambda::Function                                     UploadRequestFunction                                     -
CREATE_IN_PROGRESS                                        AWS::ApiGatewayV2::Api                                    MyApi                                                     -
CREATE_IN_PROGRESS                                        AWS::ApiGatewayV2::Api                                    MyApi                                                     Resource creation Initiated
CREATE_COMPLETE                                           AWS::ApiGatewayV2::Api                                    MyApi                                                     -
CREATE_IN_PROGRESS                                        AWS::Lambda::Permission                                   UploadRequestFunctionUploadAssetAPIPermission             -
CREATE_IN_PROGRESS                                        AWS::ApiGatewayV2::Stage                                  MyApiApiGatewayDefaultStage                               -
CREATE_IN_PROGRESS                                        AWS::ApiGatewayV2::Stage                                  MyApiApiGatewayDefaultStage                               Resource creation Initiated
CREATE_IN_PROGRESS                                        AWS::Lambda::Permission                                   UploadRequestFunctionUploadAssetAPIPermission             Resource creation Initiated
CREATE_COMPLETE                                           AWS::ApiGatewayV2::Stage                                  MyApiApiGatewayDefaultStage                               -
CREATE_COMPLETE                                           AWS::Lambda::Permission                                   UploadRequestFunctionUploadAssetAPIPermission             -
CREATE_COMPLETE                                           AWS::CloudFormation::Stack                                s3uploader-lambda                                         -
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

CloudFormation outputs from deployed stack
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Outputs
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Key                 APIendpoint
Description         HTTP API endpoint URL
Value               https://uzgemq6u9l.execute-api.us-west-2.amazonaws.com

Key                 S3UploadBucketName
Description         S3 bucket for application uploads
Value               s3uploader-lambda-s3uploadbucket-1y86jl8uwcvv
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Successfully created/updated stack - s3uploader-lambda in us-west-2
```
---
### aws ui 화면에 가서 lambda가 잘 생성되었는지 확인 !



---
### 파일업로드 할 정적 페이지 index.html 올릴 s3 생성 ( 나는 s3-html-index 로 만듬 )
- index.html 올리기전에 수정하기
28 line 에 const API_ENDPOINT = 'https://uzgemq6u9l.execute-api.us-west-2.amazonaws.com/uploads'  << 수정
- 해당 경로에 amazon-s3-presigned-urls-aws-sam/frontend/index.html 인 index.html 파일 업로드


---
### 업로드 후에 정적페이지 s3 index.html 호스팅 방법 아래 링크 보고 똑같이 따라하기.
```
https://longtermsad.tistory.com/44
```

### index.html을 위한 s3 버킷 정책
```
{
    "Version": "2012-10-17",
    "Id": "Policy1708065178836",
    "Statement": [
        {
            "Sid": "Stmt1708065121679",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::s3-html-index/*"
        }
    ]
}
```

## 모든 자원 삭제 ( s3 버킷 안에 내용 다 삭제 한 후에 )
```
cd amazon-s3-presigned-urls-aws-sam
sam remove
```
