# Architecting on AWS
- Module 01 - 아키텍팅 기본 사항
    1. AWS 서비스에 연결
        1. AWS 관리 콘솔: Username/Password
        2. AWS CLI: Access key(AK/SK)
        3. AWS SDK: Access key(AK/SK)
    2. 관리형 서비스 vs. 비 관리형 서비스
    3. AWS 인프라
        1. 데이터 센터를 모아서 가용영역으로, 가용영역 2개를 모아서 리전으로
        2. 리전표기: 도시이름과 지역명으로 표기 ex. 서울리전 or ap-northeast-2
        3. 가용영역 표기: 리전의 지역명 표기 끝에 영문자를 추가하여 표기 ex. ap-northeast-2a, ap-northeast-2b...
        4. Edge Location: PoP(Point of Presence); 전 세계 주요 도시에서 운영; Route53 및 CloudFornt 같은 서비스가 지원
        5. Local Zone: Edge location의 AWS infrastructure; AWS 리전의 서비스와 연결되어 있고 지연 시간이 짧은 애플리케이션 제공을 위해 사용
    4. 리전 선택의 요인: 거버넌스(규정준수); 지연시간; 서비스 가용성; 비용
    5. AWS Well-Architected: 클라우드 아키텍처를 이해하고, 측정하고 모범 사례를 통해 아키텍처 구축에 도움
        1. WAF: 6개 항목: 보안, 비용최적화, 안정성, 성능효율성, 운영우수성, 지속가능성
        2. AWS Well-Architected Tool: 워크로드를 파악하고 아키텍처 검토, 개선 계획 검토, 문제해결 계획 수립
    6. References
        1. [https://aws.amazon.com/ko/about-aws/global-infrastructure/](https://aws.amazon.com/ko/about-aws/global-infrastructure/)
- Module 02 - 계정보안
    1. 계정 Root User: 전체 엑세스 권한 보유, 일반적인 사용에 사용하면 안됨
    2. IAM: 인증과 권한부여
        1. User, Group, Role 생성관리
        2. AWS 서비스 및 리소스에 대한 액세스 관리; 액세스 제어 분석
        3. 디렉토리 서비스와 통합
    3. 보안주체(Principal): AWS 리소스에 작업을 요청할 수 있는 권한을 가진 entity
    4. IAM User: 계정 내의 사용자(EC2 인스턴스나 DB 내의 사용자 X)
        1. 각 사용자는 자체 보안인증(Credential) 정보가 있고, 권한에 따라 특정 AWS 작업을 수행할 수 있음
    5. 사용자 보안 인증 정보(Credential)
        1. Root user: Email Address/Password *optional* MFA(Multi Factor Authentication)
        2. IAM user - Console: Username/Password *optional* MFA
        3. IAM user or other - CLI, SDKs: Access key(Access Key ID, Secret Access Key) *optional* MFAAccess key 는 CLI 의 경우 aws configure 로 등록하고, SDK 의 경우 (대개) 지정된 파일에 access key 를 등록
    6. IAM Group: User에 대한 권한 할당을 상속 형태로 간편화하기 위해 사용
    7. IAM Policy: 하나 이상의 권한(Permission)을 잘 정리한 것으로 권한부여에 사용
    8. IAM Role: 특정 사용자 또는 서비스에 특정 권한을 위임하고, 보안인증 정보를 (내부적으로 처리되기 때문에) 다른 사용자와 공유하지 않고 수임하여 권한을 가지고 작업을 하는 동안만 유효(Temporary)
        1. AWS STS(Security Token Service): Role 수임에 대한 호출에 대해 처리하여 임시 credential 정보를 서비스
        2. Role=모자: Trust Relationship(누가 이 모자를 쓸 수 있는 지의 권한) + Permission(모자를 썼을 때 얻어지는 권한)
        3. 이점: 교차 계정 액세스 권한부여, 조직내 IdP 를 통한 권한부여, Application 이 안전하게 API call 을 이용, AWS 서비스가 (사용자 대신)다른 서비스를 호출 가능
    9. Policy 유형: 크게 권한을 부여하는 정책과(Grant permissions) 사용 가능한 권한의 범위와 제한에 대한 최대 권한 설정(Set maximum permissions
        1. 놀이공원의 자유이용권은 grant permission 이고, 후렌치레볼루션의 키 120cm 제한은 set maximum permissions(safe guard)
        2. Identity-based policy: User, Group, Role
            1. Managed vs. In-line
        3. Resource-based policy: S3 서비스와 같은 리소스에 직접 부여
    10. Policy 권한평가: 1/명시적 거부, 2/명시적 허용, 3/묵시적 거부, 묵시적 허용(존재X)
    11. Policy conditions: Optional 하게 사용하여 어떤 조건이 되었을 때 정책이 적용될 수 있도록 함
    12. Access Advisor: 서비스에 대한 권한 액세스가 일어난 최근 파악
    13. AWS IAM Access Analyzer: 외부 entity 와 공유되는 리소스 파악용이
    14. IAM 권한 경계(permissions boundaries)
    15. SCP(Service Control Policy): AWS Organizations 서비스에서 계정을 통제하기 위해 사용(Safe guard)
        1. 특정 OU에서 특정 (비용이 비싼) 서비스를 못 띄우게 하거나 혹은 특정 작업(인스턴스 셧다운)을 못하도록 방지
        2. 내리상속의 특성; IAM 권한과 같이 쓰여 마치 교집합(condition 과 같은 역할)으로 권한 통제
    16. Federation 사용자: AWS 와 사전에 서로 인증정보를 공유하겠다 Federation 을 맺고 서비스 이용에 따른 권한은 Role로 제공
        1. SAML 2.0, OIDC, AWS Cognito
    17. AWS SSO: 사내 인증 정보(ex: active directory) 혹은 3rd-party(ex: SFDC, Box, Office365)와 AWS 계정의 페더레이션 인증을 통합하고 서비스 이용에 따른 권한은 Role을 통해 제공
    18. AWS Organization: OU 단위로 Member 계정을 할당하고 SCP 를 통해 통제; 통합 과금(billing management)
    19. AWS Control Tower: AWS Organizations 이용하여 멀티 어카운트 구성을 안전하고 규정을 준수하도록 구축할 수 있는 서비스
- Module 03 - 네트워킹 1
    1. VPC는 Private IPv4 주소(default) 또는 IPv6 를 사용할 수 있고 CIDR 표기법 이용
        1. 단일 AWS 리전에 설정; 워크로드의 논리적 격리를 제공, 보안설정 가능; 사용 가능한 IP 주소의 범위를 설정
        2. Default VPC: 계정 생성 시 모든 리전에 자동적으로 만듬(IGW 포함)
    2. Subnet: VPC CIDR 블록의 하위집합, 하나의 AZ에 위치하고 여러 AZ에 걸칠 수 없음; 서브넷간 중첩 불가
        1. Public/Private 종류가 있는건 아니고, 인터넷에 노출여부로 지칭
    3. 공인(Public) IP는 EC2 인스턴스를 론칭할 때 부여하는 Public IP(PIP)와 Elastic IP(EIP) 2가지
        1. PIP는 인스턴스 stop/start 후 IP 주소가 변경됨; EIP는 변경되지 않음
        2. EIP는 인스턴스 또는 ENI에 연결할 수 있고, 고정IP 이므로 서버의 서비스용 IP 로 이용할 수 있고 리전당 5개로 제한(soft)
    4. Internet Gateway: 인스턴스와 인터넷간 (양방향) 통신 허용; 서브넷과 연결된 route table에 등록필요
    5. NAT Gateway: private subnet의 private IP 주소를 가진 인스턴스가 outbound로 인터넷을 이용할 수 있게끔
    6. Route table: VPC를 만들면 main route table이 자동적으로 만들어 지고, (대개) main route table 은 private subnet 이 참조하는 route table 로 이용; public subnet은 사용자 지정 route table을 별도로 만들어 association
        1. main route table을 포함 모든 route table에는 default route(*VPC_IP_CIDR local*) 가 등록되는데 삭제할 수 없고, 모든 사용자지정 route table 생성 시 기본적으로 추가; default route로 인해 VPC내 모든 인스턴스는 서로간 통신가능
    7. ENI(Elastic Network Interface): 동일AZ내에서 리소스간 이동가능; Private, Public, MAC 주소를 유지
    8. Egress-only internet gateway(송신전용 인터넷 게이트웨이): 쉽게말해, IPv6 용 NAT Gateway
    9. Network ACL: 서브넷의 방화벽; 아웃바운드, 인바운드가 열린 상태로 시작; 낮은 규칙번호 부터 적용, 마지막은 *; stateless
    10. Security Group: 인스턴스(ENI) 방화벽; 아웃바운드는 열리고 인바운드는 닫힌 상태로 시작; IP주소, 프로토콜, 포트기반 트래픽제어, stateful
        1. Security Group Chaning: Source를 다른 SG 로 참조하여 마치 체인의 연결처럼 SG를 구성하여 더욱 안전한 보안구성
    11. References
        1. VPC Designer: [https://vpcdesigner.com/](https://vpcdesigner.com/)
    
    IPv4 Planner:
    
    [https://network00.com/NetworkTools/IPv4AddressPlanner/](https://network00.com/NetworkTools/IPv4AddressPlanner/)
    
- Module 04 - 컴퓨팅
    1. AMI(Amazon Machine Image): Pre-built, AWS Marketplace, 자체생성(My AMI), Community AMI
    2. EC2 Image Builder: AMI 생성 자동화
    3. Multi-Tenancy(shared tenancy) vs. Single-Tenancy: 기본적으로 multi-tenancy, 전용인스턴스 및 전용호스트로 single-tenancy
        1. dedicated instance의 경우 같은 계정내 일반 인스턴스와 hardware를 공유할 수도 있다
        2. dedicated host 의 경우 socket 확인이 가능해 이를 요구하는 라이센스 환경에 이용할 수 있다.
    4. Instance type: FG[].S|Family-Generation-Attribute.Size
        1. 범용, 메모리최적화, 스토리지최적화, 컴퓨팅최적화, 엑셀러레이티드 컴퓨팅
    5. AWS Compute Optimizer: 기계학습을 사용하여 최적의 인스턴스를 사용할 수 있도록 권고
        1. EC2, EBS, Lambda
    6. User data | Instance metadata | EC2 key pairs
    7. EC2 구매옵션: Reserved Instances, Savings Plans, Spot Instances, On-Demand Instances
    8. EBS vs Instance Store(Storage): 데이터 휘발성 여부, snapshot 백업 가능여부
    9. EBS volume types: SSD 기반 vs. HDD 기반
        1. SSD 기반: 범용, Provisioned IOPS
        2. HDD 기반: 처리량 최적화, 콜드; OS 볼륨X
    10. AWS 기반 HPC
        1. 컴퓨팅: 인스턴스, 스팟인스턴스, EC2 Auto Scaling
        2. 네트워킹: 향상된 네트워킹, 배치그룹(Placement group), EFA(Elastic Fabric Adapter)
            1. 배치그룹: 클러스터, 파티션, 분산
        3. 자동화 및 오케스트레이션: AWS Batch, AWS ParallelCluster
        4. 스토리지: FSx for Lustre, EFS, S3, EBS
    11. Lambda: 서버리스 컴퓨팅, 다양한 코드지원, 최대 15분간 실행, 최대 10GB 메모리 지원(메모리에 비례해서 자동적으로 CPU할당)
        1. 주로 이벤트소스가 되는 다른 서비스를 통해 호출되어 사용