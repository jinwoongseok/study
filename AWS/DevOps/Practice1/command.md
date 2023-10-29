1. 키 페어 이름을 검색하고 이를 사용해 환경 변수를 생성
```
keyPair=$(aws ec2 describe-key-pairs --output text --query KeyPairs[*].KeyName)
```

2. 스택 생성 프로세스 시작하는 커맨드
```
aws cloudformation create-stack --stack-name AWSStudent-Lab1 \
--parameters ParameterKey=KeyName,ParameterValue=$keyPair \
ParameterKey=InstanceType,ParameterValue=t2.micro \
--template-body file://[simple-infrastructure-file-name]
```
- 예상 출력
```
******************************
**** This is OUTPUT ONLY. ****
******************************

{
    "StackId": "arn:aws:cloudformation:ca-central-1:350214907448:stack/AWSStudent-Lab1/7f733ee0-bf54-11ec-aa09-0244448197b2"
}
```
3. describe-stacks 명령은 템플릿에 정의한 모든 리소스, 빌드 프로세스의 현재 상태 등을 제공한다.
```
aws cloudformation describe-stacks --stack-name AWSStudent-Lab1
```
4. 드리프트 발생 시 확인하는 커맨드
```
aws cloudformation describe-stack-resource-drifts --stack-name AWSStudent-Lab1 --stack-resource-drift-status-filters MODIFIED DELETED
```

5. Template에서 변경 사항 추가 시 change set을 통해 스택 변경하는 프로세스
```
aws cloudformation create-change-set --stack-name AWSStudent-Lab1 \
--change-set-name Lab1ChangeSet \
--parameters ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=KeyName,ParameterValue=$keyPair \
--template-body file://[simple-infrastructure-CS-file-name]
```
- 예상 출력
```
******************************
**** This is OUTPUT ONLY. ****
******************************

{
    "Id": "arn:aws:cloudformation:ca-central-1:350214907448:changeSet/Lab1ChangeSet/b62f9a0f-bd5e-4988-9016-bba2c46f351b",
    "StackId": "arn:aws:cloudformation:ca-central-1:350214907448:stack/AWSStudent-Lab1/7f733ee0-bf54-11ec-aa09-0244448197b2"
}
```
6. 변경 세트 커밋
```
aws cloudformation execute-change-set --stack-name AWSStudent-Lab1 --change-set-name Lab1ChangeSet
```
7. 변경 세트 삭제
```
aws cloudformation delete-change-set --change-set-name Lab1ChangeSet --stack-name AWSStudent-Lab1
```