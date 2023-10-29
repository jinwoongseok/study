1. SAM 초기화
```
sam init --runtime python3.9
```
2. SAM 빌드
```
sam build
```
3. invoke AWS lambda function
```
sam local invoke HelloWorldFunction --event events/event.json
```
4. start-api를 로컬에서 사용하면 애플리케이션이 임시 컨테이너로 배포된다. Preview 기능으로 애플리케이션을 안전하게 볼 수 있다.
```
sam local start-api -p 8080
```
5. 버킷 이름을 나타내는 변수 생성
```
labBucket=lab4-sam-[YOUR-INITIALS]-[YOUR-POSTAL-CODE]
```
6. 전역적으로 고유한 버킷 생성
```
aws s3 mb s3://$labBucket
```
7. 애플리케이션을 패키징하고 생성한 S3 버킷에 푸시
```
sam package --output-template-file packaged.yaml --s3-bucket $labBucket
```
8. SAM 배포하기
```
sam deploy --template-file packaged.yaml --stack-name sam-app --capabilities CAPABILITY_IAM
```
9. AWS PipeLine을 통해 배포하기 위해 서버리스 애플리케이션 준비(s3 생성해놓고 버킷명 변수에 저장)

[buildspec.yml]
```
**********************************
**** This is an EXAMPLE ONLY. ****
**********************************

version: 0.2
phases:
  build:
    commands:
      - export BUCKET=[YOUR BUCKET NAME]
      - aws cloudformation package --template-file template.yaml --s3-bucket $BUCKET --output-template-file outputtemplate.yaml
artifacts:
  files:
    - outputtemplate.yaml
```
[template.yaml]
```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: >
    sam-app

    Sample SAM Template for sam-app
    
# More info about Globals: https://github.com/awslabs/serverless-application-model/blob/master/docs/globals.rst
Globals:
    Function:
        Timeout: 3


Resources:

    Lab4Function:
        Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
        Properties:
            CodeUri: lab-app/
            Handler: app.lambdaHandler
            Runtime: nodejs12.x
            AutoPublishAlias: live
            DeploymentPreference:
              Type: Linear10PercentEvery1Minute
            Events:
                WebPage:
                    Type: Api # More info about API Event Source: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#api
                    Properties:
                        Path: /hello
                        Method: get
# Deployment preference types at https://github.com/awslabs/serverless-application-model/blob/master/docs/safe_lambda_deployments.rst
Outputs:

    # ServerlessRestApi is an implicit API created out of Events key under Serverless::Function
    # Find out more about other implicit resources you can reference within SAM
    # https://github.com/awslabs/serverless-application-model/blob/master/docs/internals/generated_resources.rst#api
    Lab4Api:
      Description: "API Gateway endpoint URL for Prod stage for Lab4 function"
      Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod/hello/"

    Lab4Function:
      Description: "Lab4 Function ARN"
      Value: !GetAtt Lab4Function.Arn

    Lab4FunctionIamRole:
      Description: "Implicit IAM Role created for Lab4 function"
      Value: !GetAtt Lab4FunctionRole.Arn
    
    Lab4LatestVersion:
      Description: "Checking the version ID of the new version"
      Value: !GetAtt Lab4Function.Version.Version
```
10. buildspec.yml 파일 AWS CodeCommit 레포지토리를 업데이트
```
git add .
git commit -m "Updated buildspec file with bucket name"
git push
```
11. 이후 AWS CodePipeline 설정한다.
12. 만약 변경사항이 있을 시 AWS CodeCommit 레포지토리를 업데이트하면 AWS CodePipeline에서 이를 감지해 자동으로 배포한다.