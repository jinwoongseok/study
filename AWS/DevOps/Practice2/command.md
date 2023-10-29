1. EC2 인스턴스 생성 시 CodeDeploy Agent 세팅
```
<powershell>
Read-S3Object -BucketName aws-codedeploy-us-west-2/latest -Key codedeploy-agent.msi -File c:\temp\codedeploy-agent.msi
Start-Sleep -s 60
Start-Process c:\temp\codedeploy-agent.msi '/qn /l*v c:\temp\host-agent-install-log.txt'
</powershell>
```
2. appspec.yml
```
version: 0.0
os: windows
files:
  - source: Heartbeat.dll
    destination: c:\HeartbeatService
  - source: HeartbeatService.exe
    destination: c:\HeartbeatService
  - source: HeartbeatService.exe.config
    destination: c:\HeartbeatService
  - source: log4net.dll
    destination: c:\HeartbeatService
  - source: Logger.dll
    destination: c:\HeartbeatService
  - source: wintail.exe
    destination: c:\temp

hooks:
  ApplicationStop:
    - location: uninstall.ps1
      timeout: 30
  AfterInstall:
    - location: install.ps1
      timeout: 30
    - location: copywintail.ps1
      timeout: 30
```
appspec.yml 파일은 .zip 아카이브에 포함된 각 소스 파일을 개략적으로 설명하고 파일을 저장해야하는 대상 EC2 인스턴스의 대상을 지정한다.  

3. CodeDeploy에서는 배포 아티팩트가 S3 버킷에 저장되어야 한다. 다음은 S3를 생성하는 과정이다.
```
bucketName=heartbeat-codedeploy-artifacts-[your-initials]-[your-postal-code]
```
```
aws s3 mb s3://$bucketName
```
4. 파일을 번들링하고 이전 단계에서 생성한 S3 버킷에 푸시하고 CodeDeploy에 정보를 등록하려면 다음 명령을 실행한다.
```
aws deploy push --application-name CodeDeploy-Demo --source HeartBeat-App --s3-location s3://$bucketName/HeartBeat-App.zip
```
5. S3 버킷에서 EC2 인스턴스로 애플리케이션을 배포하려면 다음 명령을 실행한다.
```
aws deploy create-deployment --application-name CodeDeploy-Demo --deployment-group-name HeartBeat-Deployment --deployment-config-name CodeDeployDefault.AllAtOnce --description "Initial Deployment" --s3-location bucket=$bucketName,key=HeartBeat-App.zip,bundleType=zip
```
이 명령은 버킷에 HeartBeat-App.zip이라는 애플리케이션 개정을 사용해 배포한다. AllAtOnce 옵션을 사용했으므로 모든 EC2 인스턴스에 배포된다.