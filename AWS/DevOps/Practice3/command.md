### 본 실습에서는 s3와 AWS CodeDeploy의 연동을 통해 s3에 이벤트 발생 시 자동으로 CodeDeploy에서 지정한 deployment group에 배포한다. 중요한 점은 CodeDeploy는 tag기반({key, value})로 배포한다는 점이다. 이 실습에서는 명령어들만 정리했다.
1. 계정에서 애플리케이션 소스 버킷을 포함하는 버킷을 찾는다.
```
aws s3api list-buckets --output text --query 'Buckets[?contains(Name,`applicationsourcebucket`)].Name'
```
2. 해당 버킷 이름으로 변수를 생성한다.
```
myAppSrcBucket=$(aws s3api list-buckets --output text --query 'Buckets[?contains(Name,`applicationsourcebucket`)].Name')
```
3. local 파일을 s3 버킷으로 이동한다.
```
aws s3 cp ~/environment/CodeDeployHeartbeatDemo/CodeDeployHeartbeatDemo.zip s3://$myAppSrcBucket/HeartBeat-App.zip
```