# Project Description for Implementation

- deploy frontend using CLoudfront + S3 Bucket
- when user upload pdf and submit form its uplopaded on s3 bucket
- need dynamodb table
- nedd one S3 bucket for Remote Backend

## How Many Buckets Required

- 1 bucket for Remote Backend  #thi we will create manually
- 1 bucket for frontend code upload
- 1 bucket for backend image upload

## CloudFront

- we are using cloudfront for static website hosting
- hosting we will do from s3 bucket and then deploy from cloudfront
- so it will provide one CDN link
- you can access website using that link

![CloudFront](images/cloudfront-concepts.png)

## Step by Step Execution

- Clone  Repo: https://github.com/sonam-niit/Devops-Capstone-June-2025

## Create Bucket for Remote Backend

```bash
aws s3api create-bucket \
--bucket devops-accelerator-platform-tf-state-sonam \
--region us-east-1
```

## Create DynamoDB Table for Locking

```bash
aws dynamodb create-table \
--table-name devops-accelerator-tf-locker \
--attribute-definitions AttributeName=LockID,AttributeType=S \
--key-schema AttributeName=LockID,KeyType=HASH \
--billing-mode PAY_PER_REQUEST \
--region us-east-1
```

## Creating zip file for lambda functions

```bash
cd backend/generate-presigned-url/
zip -r lambda.zip .

cd backend/process-uploaded-file/
zip -r lambda.zip .
```

## Creating pipelines for Backend Frontend and Terraform

- go to your Repository and do below step
- before that we need to setup some secrets in github Repository
- you can store then in Repository Secrets

- go to Repository: Settings
- Secrets and Variables: Actions

- click on Add Repository Secrets
![RepoSecrets](images/repository-secrets.png)

- If you don't have access key and secret key
- IAM -> create user with policy attached (administrator access) -> create User
- once user created you can see option to generate Access Key
- click on that select CLI and generate. (make sure you download CSV file)


- Once All updates done
- push the code

- Once you push code on github: your terraform pipeline will executed successfully
- and you can see all resources generated.

- from that copy Presigned URL:
- go to index.html

![Url Update in Index.html](images/url-update.png)

- once its updated go to your frontend bucket and upload index.html file there

- once this is saved you can check your terraform output and take cloudfront url to see its working or not

![Outputs of Terraform](images/outputs.png)

- access cloudfrontURL in browser and try to upload image

![Upload Error](images/upload%20error.png)

+ Setp 1

- bucket (upload Bucket) --> Permissions --> CORS Policy - add Policy Code

```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "PUT",
            "POST",
            "DELETE",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": []
    }
]
```

- save the permission
- If still Getting Error

- AWS Console: APIs
    - Go to DevOps Accelerator PreSigned APIs
    - Left side -- protect -- throttling
    - edit default stage:
        + burst limit: 100
        + Rate Limit: 200
        + Save


- Check Email for Subscription Confirmation
![Confirm Subscription](images/subscription.png)

- try to upload Again
![upload successful](images/Upload%20Successfull.png)

## Verify

- in email
- cloudwatch Logs
- upload bucket and see file uploaded or not