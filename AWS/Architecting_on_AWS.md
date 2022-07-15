# Architecting on AWS
<details>
<summary>Module 01 - 아키텍팅 기본 사항</summary>
<div markdown="1">  

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
</div>
</details>

<details>
<summary>Module 02 - 계정보안</summary>
<div markdonw="1">  

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
</div>
</details>

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

- Module 05 - 스토리지
    1. S3: 내구성이 뛰어난 객체 스토리지 솔루션; 백업 및 복원, 비즈니스 크리티컬 애플리케이션, 아카이빙 및 규정준수
        1. 버킷에 객체를 무한으로 저장가능; folder라 쓰고 prefix 라 읽는다; 객체 Key로써 객체를 인식
        2. All block public access: ACL, Bucket policy, CORS 를 하나의 옵션으로 모든 public 액세스를 차단
        3. 버전관리(Versioning): 삭제와 over-write 상황에서 파일을 보존할 수 있음
        4. 스토리지 클래스: Standard, Standard IA(Infrequent Access), One Zone IA(Infrequent Access), Intelligent-Tiering, Glacier Instant Retrieval, Glacier Flexible Retrieval(*Formerly S3 Glacier*), Glacier Deep Archive**S3 Glacier Instant Retrieval**에서는 분기당 한 번 데이터에 액세스하는 경우, S3 Standard-Infrequent Access(S3 Standard-IA) 스토리지 클래스를 사용할 때와 비교하면 최대 68%의 스토리지 비용을 절감할 수 있습니다. ;의료이미지, 뉴스미디어 자사, 사용자 생성 콘텐츠와 같이 즉각적인 액세스가 필요한 아카이브 데이터에 이상적
        5. S3 Glacier (GFR): Archive 를 Vault 에 저장; 장기 보관 데이터에 대한 비용 효율적이고 내구성이 높은 스토리지
        6. S3 GFR: 신속 - 표준 - 대량
        7. S3 수명 주기 정책(Lifecycle): 데이터의 수명주기에 따라 스토리지 클래스 전환
        8. 저장 시 암호화(SSE(Server-side encryption): SSE-S3, SSE-KMS, SSE-C
        9. 객체 복제(Replicating): S3 버킷 전체에 걸쳐 객체를 비동기로 복사; 동일 리전간 복제, 교차 리전간 복제
        10. 이벤트알림(Event Notification): 이벤트 기반의 트리거링으로 람다등과 상호 동작
        11. Transfer Acceleration: 엣지 로케이션을 이용하여 데이터를 빠르게 전송
        12. 액세스 포인트: 대규모 액세스 고유한 DNS 주소로 관리; 각 포인트별로 고유한 권한 및 네트워크 제어
        13. S3 Object Lambda: Object Lambda Access Point로 접속하면 Lambda 함수가 수행되어 오리지널 데이터를 처리하여 불필요한 데이터를 빼거나, 추가하여 애플리케이션에 제공
    2. EFS: NFSv4 프로토콜, 탑재대상(mount target)을 통해 액세스, Windows 인스턴스는 지원하지 않음
        1. IP Address or DNS 로 액세스
    3. FSx for Windows File Server: SMB 프로토콜을 이용하여 Windows 서버간 파일 공유지원
    4. FSx for Lustre: 대규모 용량/성능을 지원하는 분산 파일시스템; S3 버킷과 통합하여 읽기/쓰기 가능
    5. AWS Snow Family:
        1. Snowcone: 8TB 스토리지 장착 ;Edge Computing 환경 제공
        2. Snowball Edge : 페타바이트 규모 데이터 전송 ;EC2 & Lambda 수행
        3. Snowmobile : 엑사 바이트 규모 전송
    6. DataSync: 에이전트를(VMware, KVM, EC2 instance 등등) 통해 S3 또는 EFS 로 온프레미스 데이터를 copy.
    7. Storage Gateway: 온프레미스 IT 환경과 AWS 스토리지 인프라 사이 통합 서비스
        1. File Gateway: S3 File Gateway(NFS), FSx File Gateway(SMB)
        2. Volume Gateway: Cached mode, Stored mode; iSCSI
        3. Tape Gateway: VTL(Virtual Tape Library); iSCSI
    
- Module 06 - 데이터베이스 서비스
    1. (관계형 데이터베이스 vs. 비관계형 데이터베이스) vs. (관리형 데이터베이스 vs. 비관리형 데이터베이스)
    2. RDS: HW, OS 및 DB 배포 및 유지관리; 저장 시 및 전송 중 암호화; Multi-AZ, Read Replica; 손쉬운 컴퓨팅 및 스토리지 조정
        1. 6개 엔진: Aurora(2개 인터페이스-MySQL, PostgreSQL Compatible), PostgreSQL, MySQL, MariaDB, Oracle, SQL Server
        2. Aurora: MySQL & PostgreSQL compatible; 3개의 가용영역에 각각 2개의 사본을 유지; 최대 15개 RR 구성; Compute Node와 Storage Node가 분리
        3. Multi-AZ: Master - Standby; HA 환경; 동기식 복제
        4. Read Replicas: Read only; 읽기 성능 개선; 비동기식 복제
    3. DynamoDB: 완전 관리형 NoSQL 데이터베이스; Key-Value 데이터모델; partition key 로 분할되고 스케일링되어 일관된 성능을 제공
        1. table, item, attribute
        2. primary key: partition key or composite key (partition key+sort key)
        3. consistency options: Eventually consistent vs. Strongly consistent
        4. Global table: 리전당 한 개의 복제 테이블, 모든 복제본은 동일한 테이블 이름과 동일한 primary key 스키마
    4. Redshift: 관계형 데이터를 분석하는 데 최적화된 Data warehouse; 데이터 구조 및 스키마는 빠른 SQL 쿼리를 위해 최적화되도록 미리 정의; 일반적으로 운영 보고 및 분석에 사용; the single source of truth 역할
        1. Use cases: 비즈니스 인텔리전스; 이벤트에 따른 운영분석; 서비스형 데이터; 예측분석
        2. 열 기반 스토리지(columnar storage) 사용: 데이터 집계, 분석 쿼리에 적합
        3. 동시성 스케일링(concurrency scaling): main 클러스터의 일시적인 읽기 쿼리의 갑작스런 증가 시 자동적으로 추가 클러스터를 확장하여 대응
        4. 클러스터 노드 유형: 스토리지 확장성-RA3; 처리성능우선-DC
    5. Database Caching
        1. 캐싱전략: AWS 서비스에 내재되어 있는 선택기능은 아니고 애플리케이션 캐시 사용 시 고려사항
            1. Lazy-loading: cache-miss 시에 캐시에 저장; 원본 데이터의 업데이트 변화 시 stall 될 수 있다
            2. Write-through: 데이터 쓰기 시 캐시에 저장; 불필요한 데이터가 캐시될 수 있다.
            3. TTL(Time to Live) 값을 통해 2가지 전략의 단점을 낮출 수 있다.
        2. ElastiCache: Memcached vs. Redis 
            1. Amazon MemoryDB for Redis
        3. DynamoDB Accelerator(DAX): DynamoDB 를 위한 완전관리형 고가용성 캐시
            1. 마이크로초 단위의 성능(초당 백만 리퀘스트처리)
    6. Database Migration Service(DMS): 한번 또는 지속적 마이그레이션 가능, Snowball Edge 이용가능, 이기종 가능
        1. 이기종 마이그레이션 시 SCT(Schema Conversion Tool)로 스키만 변환
- Module 07 - 모니터링 및 스케일링
    1. CloudWatch: 지표수집, 모니터링, 경보를 생성하여 알림, 리소스 용량 변경(auto-scaling), 대시보드
        1. 지표(metric): Standard metric vs. custom metric; 일정한 시간 간격
        2. CloudWatch Logs: 애플리케이션 로그를 모아서 저장, 분석하여 경보생성(지표필터)
            1. 로그스트림이 로그그룹으로 그룹핑
        3. 경보(alarm): 대상 metric 식별 - 경보 생성 - 액션 정의
            1. 경보 상태: OK, ALARM, INSUFFICIENT DATA
            2. 임계값이 지정된 기간을 초과할 때 경보가 발생하며 임계값 아래로 떨어지면 바로 OK로 전환
        4. Events 및 EventBridge: 이벤트 중심의 아키텍처 구현
            1. EventBridge 는 Events를 확장한 개념; 이벤트 소스가 사용자 지정 이벤트 및 SaaS 애플리케이션으로 확장
    2. CloudTrail: 계정활동을 로깅하고 모티너링, API 기록, 감사 목적
    3. VPC Flow Logs: 네트워크 인터페이스에서 송수신되는 IP 트래픽에 대한 정보를 수집
    4. ELB: 사용자 트래픽을 분산하고, 고가용성 제공, health check
        1. 리스너 - 규칙 - Target Groups - Targets
        2. 유형: ALB(L7), NLB(L4), GWLB(L3/L4), CLB(L7, L4)
    5. Auto Scaling: 조건에 따라 AWS 리소스를 시작 또는 종료, 새 인스턴스를 로드 밸런서에 등록
        1. (Application) Auto Scaling vs. EC2 Auto Scaling
    6. EC2 Auto Scaling: 시작 구성 및 시작 템플릿, Auto Scaling group, Auto Scaling policy
        1. Auto Scaling group: min, max, desired capacity
        2. Auto Scaling group 조정방법
            1. Maintaining a fixed number of instances(Steady State): 실습 4 참조
            2. Manual scaling
            3. Dynamic scaling
            4. Scheduled scaling
            5. Predictive scaling
        3. Auto Scaling group 에 예약 인스턴스(RI), 스팟 인스턴스 사용으로 비용 최적화
- Module 08 - 자동화
    1. AWS Elastic Beanstalk : 개발자가 기본 인프라에 대해 걱정할 필요 없이 클라우드에 확장 가능한 웹 애플리케이션 및 서비스를 배포하고 유지 관리할 수 있도록 도움. 서비스 자체는 무료
    2. AWS CloudFormation : AWS 리소스들을 안전하고 반복 가능하도록 구성하는 방식을 JSON 또는 YAML 형식의 템플릿 파일 사용
        1. Infrastructure as Code(IaC)
        2. Template 를 통해 CFN(CloudFormation)에 의해 만들어진 인프라 스트럭쳐가 곧 stack(논리적 모습) 이라고 할 수 있다.
            1. Resources 섹션은 mandatory
        3. 드리프트(drift) 감지 기능으로 탬플릿을 통해 stack이 만들어진 이후 사용자등에 의해 구성이 변경되는 것을 감지하는 기능
        4. Change set 기능: stack 을 업데이트 하기 전에 변경에 대해 미리보기 기능
    3. AWS CDK는 익숙한 프로그래밍 언어를 사용하여 클라우드 애플리케이션 리소스를 모델링 및 프로비저닝할 수 있는 오픈 소스 소프트웨어 개발 프레임워크. JSON 또는 YAML로 변환되어 적용됨.
    4. AWS Systems Manager : 소프트웨어 인벤토리 수집, OS 패치 적용, 시스템 이미지 생성, Windows 및 Linux 운영 체제 구성을 자동화. 
        1. Operation management, Application Management, 작업 및 변경, 인스턴스 및 노드
- Module 09 - 컨테이너
    1. 모놀리식 인프라 vs. 모놀리식 애플리케이션
        1. 모놀리식 인프라: ELB, SQS, SNS 서비스로 loosely coupling 환경으로 개선
        2. 모놀리식 애플리케이션: 마이크로 서비스로 전환
    2. X-Ray : 모던 애플리케이션을 분석하고 디버깅. (분산환경 일때는 디버깅을 도와주는 서비스)
        1. 데이터 수집 > 트레이스(trace) 기록 > 서비스 맵 확인 > 문제분석
    3. 컨테이너는 서버에 설치된 운영 체제를 공유하며 리소스가 격리된 프로세스 형태로 실행되므로 환경에 상관없이 빠르고 안정적이며 일관된 배포를 보장합니다.
    4. 가상화 및 추상화 수준 : 베어메탈 서버, 가상머신, 컨테이너 (Docker엔지)
        1. 가상머신은 격리되어 있지만 동일한 OS 및 바이너리/라이브러리를 공유하지 않고 컨테이너는 격리되어 있지만 OS를 공유하고 필요한 경우 바이너리/라이브러리를 공유
    5. 관리형 컨테이너 서비스: ECS vs. EKS
        1. EC2 or Fargate 를 이용하여 컨테이너를 운영
        2. ECR (Elastic Container Registry)에 컨테이너 이미지를 등록하여 중앙에서 서비스 및 관리
- Module 10 - 네트워킹 2
    1. VPC Endpoints : VPC 내 EC2 인스턴스가 AWS Service들(S3,DynamoDB등)을 사용시 인터넷을 통하지 않고 리전 내부적으로 Private 하게 연결
        1. Gateway Endpoint: S3, DynamoDB 만 지원
        2. Interface Endpoint: AWS PrivateLink를 지원하는 서비스에 연결
    2. AWS PrivateLink 솔루션: PrivateLink를 지원하는 AWS 서비스에 대해 Interface Endpoint를 통해 퍼블릭 인터넷을 타지 않고 내부적으로 직접 연결
        1. AWS 파트너가 호스트하는 서비스 및 Marketplace에서 제공되는 지원 솔루션에도 연결 가능
        2. 단방향 액세스, 단일 서비스에만 액세스(연결 대상이 여러개면 각각 생성), 서로 다른 VPC인 경우 주소가 겹쳐도 OK
    3. VPC Peering : VPC 와 VPC간에 1:1 연결 , 리전에 걸쳐 연결가능, AWS Account 간 연결 가능
    4. TGW(Transit Gateway): VPC 간 연결 , 온 프라이머스 와 VPC간 연결, Hub 역할, TGW's route table 을 이용하여 연결/격리
    5. Site-to-Site VPN: 인터넷을 통한 온프레미스와 VPC 연결
        1. VGW(Virtual Private Gateway)
    6. Direct Connect(DX): 전용선 서비스; DX 로케이션에서 연결
        1. Private VIF, Public VIF, Transit VIF
    7. VGW(Virtual Private Gateway) : 가상 프라이빗 게이트웨이, Amazon VPC와 다른 네트워크 사이에 프라이빗 연결(VPN)
    8. 온프라이머스 와 AWS VPC 연결은 , VPN , DX (Direct Connect ) 이용
    9. Route 53 Public hosted zone vs. Private hosted zone
        1. Routing policies
    10. 하이브리드 클라우드용 Route 53 Resolver
    
    [AWS networking fundamentals-SharedMaterials.pdf](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/366e1536-cfd6-4104-938f-1efa3aa6c49e/AWS_networking_fundamentals-SharedMaterials.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220715%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220715T000513Z&X-Amz-Expires=86400&X-Amz-Signature=655bb1a648e2815cfee219f4c271021ebf465b693ecd9469f59ee0748e51f065&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22AWS%2520networking%2520fundamentals-SharedMaterials.pdf%22&x-id=GetObject)
    
- Module 11 - 서버리스
    1. 서버리스: 프로비저닝하거나 관리할 인프라가 없음, 자동으로 크기조정, 종량제 요금, 내장된 보안-고가용성 컴퓨팅
    2. API Gateway : 애플리케이션의 "현관" 역할을 하는 API를 생성 , 최대 수십만 건의 동시 API 호출을 처리
        1. Lambda Function 과 함께 이용가능
    3. Amazon Simple Queue Service (Amazon SQS) : 완전 관리형 메시지 대기열 서비스, 메세지가 처리및 삭제 될 때까지 저장됨.
    4. 대기열은 표준|Standard 대기열과 FIFO 대기열을 제공함.
    5. 배달 못한 편지 대기열 (DLQ-Dead Letter Queue) 를 지원하여 처리되지 못한 메세지를 별도로 구분하여 문제 파악 가능
    6. 가시성 제한 시간 : SQS에 저장된 메세지를 수신하는 경우 다른 수신자가 메제지를 보지 못하도록 설정하는 시간(중복처리방지)
    7. 긴|long 폴링 기능을 통해서 메세지가 큐에 들어올때 까지 긴 시간동안 기다릴 수 있다.짧은|short 폴링에 비해 적은 폴링은로 비용을 아낄 수 있다.
    8. Amazon Simple Notification Service (Amazon SNS) : 손쉽게 알림 기능을 설정, 전송할 수 있는 웹 서비스, "게시-구독”(pub-sub) 메시징 패러다임을 따르며 “푸시” 메커니즘을 사용하여 클라이언트에 알림을 전달
    9. SNS 구독 유형 : Email, HTTP/HTTPS , SMS , SQS , Lambda 함수
    10. Kinesis Data Steams: 분석을 위해 데이터 스트림을 수집 및 저장
        1. 스트림을 생성하고 샤드 수를 지정; 스티림의 총 용량은 샤드 용량의 합계
    11. Kinesis Firehose: 데이터 스트림을 AWS 데이터 스토어에 로드
        1. Redshift, S3, OpenSearch Service, HTTP 엔드포인트, 서드 파티 서비스 공급자
    12. AWS Step Functions: Lambda Functions을 오케스트레이션 하여 State Machine 을 만들 수 있다. 시각적 워크플로를 사용한 마이크로 서비스 조정
        1. state=Lambda function
- Module 12 - 엣지 서비스
    1. AWS Edge
        1. AWS Wavelength - 5G 사업자는 엣지 로케이션으로 AWS Wavelength를 사용합니다.
        2. AWS 엣지 로케이션 - CloudFront, AWS WAF 및 AWS Shield가 여기에서 사용되는 서비스입니다.
        3. Outposts - Outposts는 온프레미스 또는 VPN에 위치할 수 있습니다.
        4. AWS Local Zones - 로컬 영역은 VPC의 확장입니다.
    2. Amazon CloudFront : 웹 사이트, API, 동영상 콘텐츠 또는 기타 웹 자산의 전송을 가속화하는 글로벌 CDN 서비스
        1. CloudFront 는 사용자에게 전달되는 다양한 Content 에 대한 Cache 서비스이다.
        2. CDN(Contenct Delivery Network) Service
        3. 콘텐츠 만료 방법 : Time To Live(TTL) , 객체 이름 변경, 객체 무효화(Invalidation)
    3. Global Accelerator:
        1. 글로벌 사용자에게 제공하는 애플리케이션의 가용성과 성능을 개선하는 네트워킹 서비스
        2. 가용성이 높고 정체 없는 AWS 글로벌 네트워크를 사용하여 사용자의 인터넷 트래픽을 AWS의 애플리케이션으로 전달하므로 일관된 사용자의 경험을 제공
        3. 애플리케이션 엔드포인트의 상태를 지속적으로 모니터링하고, 가장 가까운 정상 엔드포인트로 트래픽을 라우팅하여 애플리케이션 가용성을 개선
        4. AWS 호스팅 애플리케이션의 고정 진입점 역할을 하는 고정 IP 주소를 제공하여 글로벌 애플리케이션을 보다 쉽게 관리. 따라서 서로 다른 AWS 리전 및 가용 영역별로 특정 IP 주소를 관리하는 복잡성이 사라집니다.
    4. DDoS 공격: distributed denial of service (DDoS) attack
    5. Shield Standard: 상시 네트워크 흐름 모니터링 기능을 제공, 모든 고객들 추가 비용없이 자동보호
        1. 인프라 계층 공격: UDP reflection attack, SYN floods
    6. 애플리케이션 계층 공격: Slowloris DDos attack, HTTP flood attck(Cache-busting attacks, WordPress XML-RPC floods)
        1. DNS floods, SSL negotiation attacks
    7. AWS WAF: 애플리케이션 계층 트래픽을 검사하고 필터를 적용(HTTP 및 HTTPS)
        1. 보호대상: CloudFront, ALB, API Gateway, AppSync
        2. ACL 규칙 구문을 사용하여 트래픽 제어
            1. 공격방지, 트래픽 필터링, 패턴일치, 논리적연산
    8. AWS Firewall Manager: AWS WAF 및 Amazon VPC 보안 그룹의 운영 및 유지 관리 태스크를 단순화
        1. 계정 및 애플리케이션 전반에 걸친 규칙 관리를 단순화
        2. 자동으로 새 계정 검색 및 규정 미준수 이벤트 수정
        3. AWS Marketplace에서 AWS WAF 규칙을 배포
        4. 모든 계정에서 신속하게 공격에 대응
    9. Outposts: AWS Cloud를 온프레미스 데이터 센터로 확장; 짧은 지연 시간, 로컬 운영 요구사항 충족
        1. Enterprise Support plan 필요
        2. AWS 리소스 지원: VPC, ALB, EC2, EBS, S3, RDS, ElastiCache, ECS, EKS