<details>
<summary>Module 00 - 참고</summary>
<div markdown="1">

[실습 동영상](http://dfr20cpm485ln.cloudfront.net/Dev_ops_index.html)

</div>
</details>

<details>
<summary>Module 01 - 인프라 자동화</summary>
<div markdown="1">

1. CloudFormation
    1. Infra를 프로비저닝 할 수 있는 json/yaml문서
    2. Drift 감지: 리소스를 누군가가 수정했을 때 원본 템플릿과 차이를 확인하는 기능
    3. Change Set 생성: 새로운 버전을 배포하기전에 어떠한 리소스의 변경이 있는지 확인하는 기능
2. CloudFormation 템플릿 기본 구조
    1. Resource(Mandatory): 생성하고자 하는 리소스 입력
    2. Parameter: 입력 받고자 하는 파라미터 영역
        1. AllowedValue: 값을 지정해서 특정 값만 허용
        2. AllowedPattern: 정규식을 통해 특정 패턴만 허용
    3. Mapping: 전체 값을 나열하고 원하는 값을 필터링해서 가져갈 수 있는 기능
        1. ex) ubuntu AMI id가 각 리전별로 다름, 미리 Mapping에 두고 이를 활용
    4. Custom Resource: 동적으로 외부 리소스와 상호작용할 수 있는 기능
        1. Mapping에서 AMI가 업데이트 될 경우 수동 업데이트가 필요했지만 동적으로 처리할 경우 Custom Resource에 람다를 연결하고 해당 람다가 항상 최신 AMI-ID를 리턴하는 형태로 사용가능
3. 스택 관리 부분
    1. 중첩 스택: 일종의 공통 모듈처럼 미리 구성한 템플릿을 불러와 사용가능한 기능
    2. StackSets: 멀티 계정 또는 다른 리전에 동일한 템플릿을 배포할 때 따로 계정별로 동일한 템플릿을 생성하는 것이 아니라 StackSet을 이용해 배포하면 효과적으로 관리 가능
4. [taskcat - cloudformation 테스트 도구](https://github.com/aws-ia/taskcat/blob/main/README.md): 리소스를 실제 생성 삭제 및 보고서 생성
5. [CloudFormation으로 기존 리소스 가져오기](https://aws.amazon.com/ko/blogs/korea/new-import-existing-resources-into-a-cloudformation-stack/)
6. [실습 - CloudFormation Workshop - 인프라 자동화 관점](https://catalog.workshops.aws/cfn101/en-US)

</div>
</details>

<details>
<summary>Module 02 - AWS 도구 세트</summary>
<div markdown="1">

1. AWS를 효율적으로 다루기 위한 도구
    1. CLI
        1. CLI 설치 후 가장 먼저 하는 작업은 'aws configure' 이며 해당 명령어에서 Access Key ID, Secret Access Key, default region, output 설정
        2. aws configure 후 credential은 ~/.aws/credentials 파일로 리전 및 출력 포맷 설정은 ~/.aws/config 파일로 저장됨
    2. SDK
    3. SAM
        1. CloudFormation을 좀 더 쉽게 사용할 수 있도록 하는 도구
        2. SAM은 로컬에서 lambda를 테스트 할 수 있고, 실제 AWS 환경에 배포까지 지원한다.
    4. CDK
        1. 개발자 친화적으로 익숙한 언어로 CloudFormation 형식을 생성해 사용할 수 있다.
        2. js, ts, java, python, .net 지원

2. AWS Cloud9
    1. 웹 IDE
    2. 직접 터미널 액세스
    3. 실시간 페어 코딩 가능
    4. 서버리스(람다) 개발할 때 도움됨(배포 전 테스트 가능)

3. [자습서 - SAM CLI](https://docs.aws.amazon.com/ko_kr/serverless-application-model/latest/developerguide/serverless-getting-started-hello-world.html)
4. [실습 - CDK Workshop](https://cdkworkshop.com)

</div>
</details>

<details>
<summary>Module 03 - CI/CD 파이프라인 및 개발 도구</summary>
<div markdown="1">

1. CI/CD 정리
    1. CI(지속적 통합): 소스코드 관리 + 빌드 + 테스트를 일련의 프로세스로 체계화 시킨 것
    2. CD(지속적 전달): 소스코드를 타겟 서버에 전달하는 것
        1. 지속적 전달(Continuous Delivery): 운영 환경에 배포할 때 사람이 체크하고 배포
        2. 지속적 배포(Continuous Deployment): 운영 환경에 배포할 때 사람이 체크하지 않고 배포
2. CI 단계에서의 AWS 서비스
    1. CodeCommit : AWS에서 사용할 수 있는 Git 서비스, 서버가 필요없음
        1. 브랜치 정책
            1. Git Flow:  메인 브랜치가 있고 새로운 기능이 있을때 새로운 브랜치로 분리 및 개발 후 메인 브랜치로 병합하는 개발 방법
            2. Trunk 기반: 메인 브랜치에서 개발내용 업데이트 하고 배포하는 부분을 브랜치로 분리해 배포하는 개발 방법
        2. 자격증명
            1. Git에서 자격증명을 IAM 헬퍼로 업데이트 해야 함 (id/pwd 방식이 아님)
            2. 설정 방법
            ```
            git config -global credential.helper '!aws codecommit credential-helper $@'
            git config -global credential.UseHttpPath true
            git config --global credential.helper '!aws --profile CodeCommitProfile codecommit credential-helper $@'
            ```
    2. CodeBuild : AWS에서 사용할 수 있는 빌드 서비스(Jenkins와 유사), 서버가 필요 없음, Amazon S3 수명 주기 정책 사용
        1. BuildSpec.yml : CodeBuild 작업 지시서, 단계별로 수행할 명령 지정
            1. Install: 패키지 설치
            2. Pre_build: 빌드전 실행할 명령 (로그인, 라이브러리 설치)
            3. Build: 메일 빌드(컴파일, 테스트) 단계
            4. Post_build: Build 후 수행(빌드후 메시지 전송)

    3. CodeDeploy: 아티팩트(빌드 결과물)를 타겟 서버로 전달하는 agent 기반 서비스, 온프레미스 서버까지 전달 가능(ec2 내 에이전트 반드시 필요)
        1. CodeDeploy 구성
            1. Application > Deployment Group > Deploy
                1.Application: 가장 최상위 단위
                2. Deployment Group: Application 안에서 논리적 그룹 지정(ex: dev / test / prod)
                3. Deploy: 일회성으로 수행되는 Deploy
        2. AppSpec.yml: CodeDeploy가 수행해야 하는 작업에 대한 단계를 명시한 작업 지시서, BuildSpec.yml 파일과 마찬가지로 각 단계가 있음
            1. ApplicationStop - 애플리케이션 서버(ex: httpd)를 중지
            2. BeforeInstall - 애플리케이션의 파일이 최종 위치에 배치되기 전에 이벤트가 발생 (ex - 이전 버전 백업등)
            3. AfterInstall - 파일이 설치된 후 할 작업
            4. ApplicationStart – 애플리케이션 시작 (ex-was start)
            5. ValidateService - 모든 작업이 완료된 후 발생하며, 애플리케이션의 유효성 검사
        3. 수정버전 (또는 개정, Revision)
            1. 소스코드 파일 + AppSpec.yml 을 수정버전이라고 하고 버전관리가 됨, 소스코드 또는 AppSpec.yml 파일 변경시 수정 버전이 올라감
    4. CodePipeline : CodeCommit, CodeBuild, CodeDeploy등 전체적으로 서비스를 하나의 워크플로우로 구성하는 서비스
        1. Stage(스테이지): 스테이지에서 각 작업이 수행됨
        2. Action(작업): 스테이지에서 Action(작업) 단위로 동작됨, 작업은 병렬, 순차적 수행등 모두 지원함

</div>
</details>

<details>
<summary>Module 04 - 마이크로서비스</summary>
<div markdown="1">

1. [실습 - Monolith to Microservices with Docker and AWS Fargate](https://github.com/aws-samples/amazon-ecs-mythicalmysfits-workshop/tree/master/workshop-1#lets-begin)
2. 마이크로서비스의 정의: 잘 정의된 API로 통신하는 소규모 서비스
    1. 장점: 속도가 빠름, 기술적 자유, 장애 격리가 쉬움
    2. 단점: 네트워킹이 복잡함, 기존 트랜잭션 처리가 복잡함
3. 모놀리스 -> 마이크로서비스로 마이그레이션 시 주의할 사항
    1. 점진적으로 해야 함
    2. 서비스는 최대한 하나의 작업만 하는 형태로 간결하게 분해해야 함

</div>
</details>

<details>
<summary>Module 05 - DevOps 및 컨테이너</summary>
<div markdown="1">

[Pet vs Cattle](https://routdeepak.medium.com/a-data-engineers-perspective-on-iac-51705fa670d9)
1. 컨테이너: 논리적 격리를 통해 배포 민첩성을 끌어 올릴 수 있는 기술
2. 대표적인 기술 Docker
    1. Dockerfile : 작업 설계서
    2. Image : Dockerfile을 기반으로 만든 불변 템플릿
    3. Container : Image를 기반으로 만든 실체
    4. Registry : Image 저장소 (Docker hub, AWS ECR)
3. Orchestration : 호스트가 여러개인 분산환경에서는 컨테이너 관리를 도와주는 서비스가 필요함
4. ECS: AWS에서 자체적으로 만든 컨테이너 관리 서비스
    1. 작업 정의 : 컨테이너에 대한 명세등이 있는 설계서
    2. 서비스 정의 : 컨테이너 상태를 관리, 동일하게 배포된 여러 컨테이너를 묶어서 EndPoint 제공(ALB)
5. EKS: 쿠버네티스의 관리형 서비스
    1. 구조
        1. Control Plane: 제어를 위한 서비스들이 모여 있음
        2. 따로 유저가 관리할 필요 없음
        3. Worker Plane: EC2 클러스터, Pod(하나 이상의 컨테이너 모음)이 기동됨


</div>
</details>

<details>
<summary>Module 06 - DevOps 및 서버리스 컴퓨팅</summary>
<div markdown="1">

1. [디지털 교육 - Getting into the Serverless Mindset (Korean)](https://www.aws.training/Details/eLearning?id=69816)
2. [디지털 교육 - Introduction to Serverless Development (Korean)](https://www.aws.training/Details/eLearning?id=64901)
3. [디지털 교육 - Build and Deploy APIs with a Serverless CI/CD (Korean)](https://www.aws.training/Details/Video?id=67686)
4. [디지털 교육 - Deep Dive on AWS Fargate: Building Serverless Containers at Scale (Korean)](https://www.aws.training/Details/Video?id=67712)
5. [실습 - CI/CD Serverless CloudFormation workshop](https://v1.cicd.serverlessworkshops.io/)
6. 서버리스 서비스를 활용하면 가져갈 수 있는 이점
    1. 서비스에 집중 가능함
    2. 인프라 관리를 하지 않음
7. 대표적인 컴퓨팅 서비스
    1. Lambda
        1. 15 min, 10GB의 메모리의 제약 존재
        2. 위에 대해 제약사항이 있으면 아래 Fargate 서비스 활용
    2. Fargate
        1. 컨테이너의 서버리스 서비스
        2. ECS, EKS둘다 지원, 기존 버전은 컨테이너가 올라가는 EC2 클러스터를 유저가 관리하지만 Fargate를 활용하면 EC2 클러스터를 유저가 관리하지 않음
    3. SAM : CloudFormation의 초기 러닝 커브가 높은 부분을 줄이고자 나온 서비스, 오픈소스이며 서버리스에 대한 부분을 설정가능
        1. 템플릿, CLI 2가지 방식으로 제어 가능함
    4. StepFunction: Workflow를 자동화 할 수 있는 서비스, 서버리스 배포 또는 CI/CD 파이프라인과 같이 사용가능

</div>
</details>

<details>
<summary>Module 07 - 배포 전략</summary>
<div markdown="1">

1. [Advanced Continuous Delivery](https://www.youtube.com/watch?v=_xmYShSDDJg)
2. [Creating a Service Using a Blue/Green Deployment](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-blue-green.html)
3. [Automating Blue/Green Deployments of Infrastructure and Application Code using AMIs, AWS Developer Tools, & Amazon EC2 Systems Manager](https://aws.amazon.com/blogs/devops/bluegreen-infrastructure-application-deployment-blog/)
4. [Overview of Deployment Options on AWS - pdf](https://d1.awsstatic.com/whitepapers/overview-of-deployment-options-on-aws.pdf)
5. [Use AWS CodeDeploy to Implement Blue/Green Deployments for AWS Fargate and Amazon ECS](https://aws.amazon.com/blogs/devops/use-aws-codedeploy-to-implement-blue-green-deployments-for-aws-fargate-and-amazon-ecs/)
6. [Detect and React to Changes in Pipeline State with Amazon CloudWatch Events](https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html)
7. [실습 - CI/CD with Jenkins](https://awsdemoworkshops.s3.us-east-2.amazonaws.com/jenkins-ws/public/en/index.html?source=awesomeawsworkshops&utm_source=awesomeawsworkshops&utm_medium=listinglink)
8. [실습 - CI/CD with Fargate](https://www.notion.so/CI-CD-with-Fargate-b6030e45a0284ac99666049c5a1b6e89)->접근 안됨

9. 4가지 배포 전략
    1. In-Place: 동일한 서버에서 기존 버전 내리고 새로운 버전 배포
        1. 장점: 비용이 저렴
        2. 단점: 서비스 중단
    2. Rolling Update: 순차적으로 업데이트
        1. 장점: 서비스 중단 없음
        2. 단점: 이전 버전과 공존할 수 있음(순간적으로)
    3. Immutable: 배포시 인프라를 새로 프로비저닝
        1. 장점: 새로운 인프라이므로 관리 복잡성 제거
        2. 단점: 새로운 인프라를 프로비저닝 하기 때문에 시간이 소요됨
    4. Blue-Green: 기존 환경을 복제하고 새로운 버전을 배포, 트래픽을 일괄적으로 스위칭해서 배포
        1. 장점: 무중단 배포 가능
        2. 단점: 비용이 순간적으로 두배가 될 수 있음
    5. AWS에서 Blue-Green 배포 구현 방법
        1. Route53의 가중치 기반 라우팅을 활용하는 방법
        2. Auto Scaling Group의 축소 정책을 활용
        3. 배포 그룹을 두개를 만들고 앞의 ELB에서 대상 그룹을 변경하는 방법

</div>
</details>

<details>
<summary>Module 08 - 자동 테스트</summary>
<div markdown="1">

1. 소프트웨어 품질은 테스트가 얼마나 신뢰성 있게 수행되는지에 따라 다름
2. 테스트 자동화는 CI/CD 파이프라인과 함께 하며 높은 신뢰성 테스트를 수행 해야 함
3. 프로덕션과 동일한 환경 복제
    1. 테스트의 신뢰성을 높이기 위해서 프로덕션 환경과 동일한 환경 복제후 테스트 -> 성공하면 프로덕션 환경 배포할 가능성 높음
    2. Mocking: 마이크로서비스등에서 다른 서비스에 영향을 받지않고 API 로직을 테스트 하고 싶을 때 외부 연결을 하지않고 가상의 서버(예상되는 응답 리턴)로 요청을 보내 응답을 받는 테스트 방법
        1. WireMock
4. 테스트 종류
    1. 단위 테스트(Unit test) – 개별적으로 테스트되는 최소 단위
    2. 통합 테스트(Integration test) – 결합 단위 및 상호 작용 테스트
    3. 내결함성 테스트(Fault tolerance test) – 시스템에서 구성 요소 장애를 처리하는 방법
    4. 부하 테스트(Load test) – 과부하 시 기능 테스트
    5. 합성 테스트(Synthetic test) – 사용자 경험을 이해하기 위한 테스트
[the twelve-factor app](https://12factor.net/ko/)

</div>
</details>

<details>
<summary>Module 09 - 보안 자동화</summary>
<div markdown="1">

1. [디지털 교육 - Deep Dive on Container Security (Korean)](https://www.aws.training/Details/Video?id=67681)
2. DevSecOps: 기존 DevOps에 보안(Security)영역도 포함하는 내용
    1. 목적은? Security도 CI/CD 파이프라인에 포함, 프로그램 신뢰성 재고 및 배포 속도 향상
3. 파이프라인의 보안 vs 파이프라인에서의 보안
    1. 파이프라인의 보안: 파이프라인에 대한 제어를 누가 하는지 초점
        1. IAM을 통한 제어, 최소 권한의 원칙, 역할 활용
        2. MFA를 통한 이중 인증
        3. IAM 정책 시뮬레이터 (Management Console, CLI): 정책 생성 후 검증을 도와주는 서비스
        4. AWS Config를 통한 파이프라인 상태제어
            1. Config 룰을 통해 원하는 파이프라인 상태 유지
            Ex) 운영 배포전 CloudFormation ChangeSet 생성 후 실행해야 함
        5. [Secret Manager vs Parameter Store](https://www.1strategy.com/blog/2019/02/28/aws-parameter-store-vs-aws-secrets-manager/)
    2. 파이프라인에서의 보안: 파이프라인에서 빌드하는 애플리케이션, 이미지에 대한 보안에 초점
        1. 컨테이너는 Clair등 타사 도구를 통해 이미지 취약점 점검 필요
        2. 위협탐지 도구
            1. Security Hub: 보안에 대한 항목을 하나의 서비스로 확인 및 제어 가능
                1. GuardDuty: 알려진 취약점(포트 스캐닝등)에 대한 위협 탐지
                2. Inspector: EC2 인스턴스에 대해 취약점 점검(OS 패치 상태, 애플리케이션 취약점 등)
                3. Macie: 개인정보에 대한 부분 점검

</div>
</details>

<details>
<summary>Module 10 - 구성 관리</summary>
<div markdown="1">

1. 운영적 관점에서 시스템에 대한 전체적인 제어
    1. 인벤토리/구성 추적 : 현재 구성된 EC2 OS 정보, 애플리케이션 정보등 전체 데이터 취합 및 대시보드 제공
    2. 제어: 원하는 운영 상태를 유지
    3. 오케스트레이션: 변경사항 배포
    4. 스택 관리: 전체적인 관리
2. 구성관리에 도움을 줄 수 있는 서비스
    1. AWS Config: Rule을 통해 원하는 상태를 설정하고 현재 상태와 비교, 차이점을 확인할 수 있는 서비스
        1. AWS Managed Rule과 Custom Rule 둘다 지원
    2. AWS Systems Manager: 전체 시스템에 대한 인벤토리 구성, 오케스트레이션등 다양한 기능을 지원하는 서비스
        1. Document: 작업 지시서
        2. Automation: Document 기반으로 작업 수행
        3. Parameter Store: 변수를 저장하는 기능
            1. Plain Text 저장 가능
            2. Secure String 저장 가능
    3. AWS OpsWorks: Chef, Puppet의 관리형 서비스, 계층으로 배포되며 CloudFormation보다 좀 더 추상화된 형태로 배포가능
        1. AWS OpsWorks for Chef Automate : 관리 서버 생성 필요
        2. AWS OpsWorks for Puppet: 관리 서버 생성 필요
        3. AWS OpsWorks Stacks: Chef이나 관리서버 생성 필요 없음(서버리스)
    4. 타사 도구: Chef, Puppet, Ansible, terraform
3. AMI 빌드 전략
    1. 완전 빌드: 애플리케이션 부분까지 AMI로 구성
        1. 장점: 배포가 빨라짐
        2. 단점: AMI 수명 주기가 짧음
    2. JeOS(Just Enough OS): 필수적인 부분만 AMI로 구성
        1. 장점: AMI 수명 주기가 길어짐
        2. 단점: 배포시에 오래 걸림
    3. 하이브리드 AMI: 잘 안바뀌는 부분만 AMI 화
        1. 장점: 완전빌드와 JeOS를 적절하게 혼합
        2. 단점: 유저데이터가 길어질 경우 JeOS와 동일한 문제 발생
4. AMI도 CI/CD 또는 다른 서비스를 활용해서 빌드 가능
    1. EC2 ImageBuilder: AMI 빌드 가능한 AWS 서비스
    2. Packer : 타사 도구
    3. System Manager: Automation을 통해 골든 이미지 빌드 가능

</div>
</details>

<details>
<summary>Module 11 - 관측 가능성</summary>
<div markdown="1">

1. [Observability](https://www.youtube.com/watch?v=pAmHHQJ0zwY)
2. 관측 가능성은 모니터링이 아님
3. 관측가능성 다음 3가지를 포괄하는 개념
    1. 모니터링(Monitoring)
    2. 로깅(Logging)
    3. 추적(Tracing)
4. 모니터링을 지원하는 서비스 – CloudWatch
    1. 지표: 시간별로 수집되는 데이터
        1. AWS가 수집하는 지표
        2. 사용자가 수집하는 지표 (EC2의 메모리)
5. 로깅을 지원하는 서비스 – CloudWatch Logs
    1. CloudWatch는 로그 정보를 저장함
    2. 단순 저장보다는 활용을 권장
    3. CloudWatch Logs의 구성
        1. 로그 그룹: 가장 최상위 단위, 로그스트림이 모여 그룹이 됨
            1. 로그 스트림: 이벤트의 순서
            2. 로그 이벤트: 최하위 단위로 이벤트 레코드
    4. CloudWatch Logs Insights
        1. 쿼리 형태로 원하는 로그를 필터링하고 시각화 가능한 기능
6. 추적을 지원하는 서비스 – X-Ray
    1. 분산 시스템에서 애플리케이션 로그 추적 및 서비스 맵을 생성, 가시성을 확보할 수 있도록 도와주는 서비스
    2. X-Ray 구성
        1. 트레이스 – 서비스에서 단일 요청과 관련된 처음부터 끝까지의 데이터
        2. 세그먼트 – 단일 서비스에 해당하는 트레이스 부분
        3. 하위 세그먼트 – 서비스 내 원격 호출 또는 로컬 컴퓨팅 섹션

</div>
</details>
