# PAAS-TA\_PLATFORM\_INSTALL\_AUTOMATION\_AWS\_v1.0

## Table of Contents

1. [문서 개요](paas-ta_platform_install_automation_aws_v1.0.md#1)
   * [목적](paas-ta_platform_install_automation_aws_v1.0.md#2)
   * [범위](paas-ta_platform_install_automation_aws_v1.0.md#3)
2. [플랫폼 설치 가이드](paas-ta_platform_install_automation_aws_v1.0.md#4)
   * [플랫폼 설치 자동화 관리](paas-ta_platform_install_automation_aws_v1.0.md#5)
   * [로그인 계정 관리](paas-ta_platform_install_automation_aws_v1.0.md#6)
   * [인프라 관리](paas-ta_platform_install_automation_aws_v1.0.md#7)
   * [스템셀과 릴리즈](paas-ta_platform_install_automation_aws_v1.0.md#8)
   * [BOOTSTRAP 설치하기](paas-ta_platform_install_automation_aws_v1.0.md#9)
     * [인프라 환경 설정 관리](paas-ta_platform_install_automation_aws_v1.0.md#10)
     * [스템셀 다운로드](paas-ta_platform_install_automation_aws_v1.0.md#11)
     * [릴리즈 다운로드](paas-ta_platform_install_automation_aws_v1.0.md#12)
     * [디렉터 인증서 생성](paas-ta_platform_install_automation_aws_v1.0.md#13)
     * [BOOTSTRAP 설치](paas-ta_platform_install_automation_aws_v1.0.md#14)
   * [CF-Deployment 설치하기](paas-ta_platform_install_automation_aws_v1.0.md#15)
     * [스템셀 업로드](paas-ta_platform_install_automation_aws_v1.0.md#16)
     * [PaaS-TA 릴리즈 사용](paas-ta_platform_install_automation_aws_v1.0.md#17)
     * [CF-Deployment 설치](paas-ta_platform_install_automation_aws_v1.0.md#18)
   * [서비스팩 설치하기](paas-ta_platform_install_automation_aws_v1.0.md#19)
     * [릴리즈 업로드](paas-ta_platform_install_automation_aws_v1.0.md#20)
     * [Manifest 업로드](paas-ta_platform_install_automation_aws_v1.0.md#21)
     * [서비스팩 설치](paas-ta_platform_install_automation_aws_v1.0.md#22)

## 1.  문서 개요

### 1.1.  목적

본 문서는 플랫폼 설치 자동화 시스템의 사용 절차에 대해 기술하였다.

### 1.2.  범위

본 문서에서는 AWS 인프라 환경을 기준으로 플랫폼 설치 자동화를 사용하여 PaaS-TA를 설치하는 방법에 대해 작성되었다.

## 2.  플랫폼 설치 가이드

BOSH는 클라우드 환경에 서비스를 배포하고 소프트웨어 릴리즈를 관리해주는 오픈 소스로 Bootstrap은 하나의 VM에 디렉터의 모든 컴포넌트를 설치한 것으로 PaaS-TA 설치를 위한 관리자 기능을 담당한다.

플랫폼 설치 자동화를 이용해서 클라우드 환경에 PaaS-TA를 설치하기 위해서는 인프라 설정, 스템셀 소프트웨어 릴리즈, Manifest 파일, 인증서 파일 5가지 요소가 필요하다. 스템셀은 클라우드 환경에 VM을 생성하기 위해 사용할 기본 이미지이고, 소프트웨어 릴리즈는 VM에 설치할 소프트웨어 패키지들을 묶어 놓은 파일이고, Manifest파일은 스템셀과 소프트웨어 릴리즈를 이용해서 서비스를 어떤 식으로 구성할지를 정의해 놓은 명세서이다. 다음 그림은 BOOTSTRAP을 이용하여 PaaS-TA를 설치하는 절차이다.

![](../../.gitbook/assets/install_flow.png)

### 2.1  플랫폼 설치 자동화 관리

플랫폼 디렉터의 코드/권한/사용자 관리를 통해 전체 사용자의 생성 및 사용 권한과 공통 Manifest 버전/신규 스템셀 확장 등의 공통 코드를 사용할 수 있다.

### 2.2.  로그인 계정 관리

플랫폼 설치 자동화 웹 화면에서 “플랫폼 관리자 관리” -&gt; “로그인 계정 관리” 메뉴로 이동한다. 플랫폼 설치 자동화는 “로그인 계정 관리” 메뉴에서 기본적으로 플랫폼 설치 자동화 관리자 정보\(admin/admin\)를 제공한다.

#### 1. _로그인 계정 등록_

1. 사용자 “등록” 버튼을 클릭 후 사용자 정보 입력 및 해당 사용자의 권한을 선택하여 “확인” 버튼을 클릭한다.
2. 사용자 등록 후 초기 비밀번호는 “1234” 이며, 최초 로그인 후 비밀번호를 변경할 수 있다.

![](../../.gitbook/assets/user_add%20%284%29%20%284%29%20%283%29.png)

#### 2. _로그인 계정 수정_

1. 사용자 “수정” 버튼을 클릭 후 사용자 정보 및 해당 권한을 수정하여 “확인” 버튼을 클릭한다.
2. 관리자는 선택한 사용자의 아이디는 수정할 수 없지만 비밀번호를 변경할 수 있다.

![](../../.gitbook/assets/user_modify%20%283%29%20%283%29%20%282%29.png)

### 2.3  인프라 관리

BOOTSTRAP & PaaS-TA를 설치하기 위해서는 사전에 인프라 환경 구축이 필요하다. 인프라 관리 메뉴는 실제 인프라의 대시보드 화면에서도 환경 구축을 할 수 있다. 플랫폼 설치 자동화 웹 화면에서 “인프라 환경 관리” 의 “인프라 계정 관리” 메뉴에서 AWS 인프라 계정을 등록하고 “AWS 관리” 메뉴로 이동하여 등록 한 AWS 계정을 설정 후 해당 AWS 계정에 대한 실제 AWS 인프라 리소스를 제어할 수 있다.

![](../../.gitbook/assets/iaas_account.png)

#### 1. _AWS 계정 설정_

1. “인프라 환경 관리” -&gt; “인프라 계정 관리 화면으로 이동한다”
2. 전체 인프라 계정 관리 화면에서 AWS 계정 관리 화면으로 이동한다.
3. AWS 계정 관리 화면에서는 “등록” 버튼을 클릭한다
4. AWS 계정 등록 팝업 화면에서 플랫폼 설치에 필요한 인프라 계정 정보를 입력하고 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/aws_iaas_add.png)

※ 계정 등록 정보

* 계정 별칭: 인프라 관리에서 사용할 계정의 별칭
* Access Key Id: Identity 서비스에 대한 Key ID
* Secret Access Key: identity 서비스에 접근할 수 있는 비밀키

#### 2. _AWS 리소스 관리_

![](../../.gitbook/assets/aws_mgnt.png)

1. AWS 관리 화면에서 등록 한 AWS를 기본 계정으로 설정하여 실제 AWS 리소스를 제어할 수 있다.
2. “인프라 환경 관리” -&gt; AWS 관리 화면으로 이동한다.
3. 아래는 AWS 관리 화면에서 실제 PaaS-TA 설치에 필요한 AWS 리소스의 환경이다.

|  인프라 환경 |  메뉴 |
| :--- | :--- |
|  AWS 리소스 관리 |  VPC 관리 |
|  Subnet 관리 |  |
|  Security Group 관리 |  |
|  Elastic IP 관리 |  |
|  Key Pair 관리 |  |
|  NAT Gateway 관리 |  |
|  Route Table 관리 |  |

#### 3. _AWS VPC 등록_

1. “AWS 관리” 메뉴에서 “VPC” 메뉴를 클릭한다.
2. “생성” 버튼을 클릭한다.
3. 생성 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/vpc_add.png)

※ AWS VPC 등록 정보

* VPC Name Tag: 등록할 VPC의 별칭
* IPv4 CIDR block: IPv4의 주소형태를 가지는 네트워크 범위
* IPv6 CIDR block: IPv6의 주소형태를 가지는 네트워크 범위
* Tenancy: Tenancy 속성 지정, 이 VPC에서 시작된 인스턴스가 시작 시 지정된 속성을 사용하려면, 기본값을 사용

#### 4. _AWS Subnet 등록_

1. “AWS 관리” 메뉴에서 “Subnet” 메뉴를 클릭한다.
2. “생성” 버튼을 클릭한다.
3. 생성 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.
4. CF-Deployment/Service 등을 추가적으로 다른 서브넷 대역에 설치할 경우 동일한 VPC를 선택 후 해당 작업을 반복하여 서브넷을 생성한다.

![](../../.gitbook/assets/subnet_add.png)

※ Subnet 설정 정보

* Name Tag: 서브넷의 별칭
* VPC: 서브넷의 기준이 되는 VPC 정보\(3 항목에서 생성한 VPC\)
* VPC CIDRs: VPC의 CIDR 정보\(자동 입력\)
* Availability Zone: Region 내의 개별적인 지점으로, 같은 Region 내의 다른 가용 영역에 비해 저렴하고 지연 시간이 짧은 네트워크 연결을 제공
* IPv4 CIDR block: 서브넷의 네트워크 범위

**여기서 BOSH를 위한 서브넷이 생성되었으나, CF를 위한 서브넷 하나가 더 필요하므로 서브넷 하나를 추가 생성해준다.**

#### 5. _AWS Internet Gateway 등록_

1. “AWS 관리” 메뉴에서 “Internet Gateway” 메뉴를 클릭한다.
2. “생성” 버튼을 클릭한다.
3. 게이트웨이 생성 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/internet_gateway_add.png)

※ 게이트웨이 등록 설정 정보

* Internet Gateway Name tag: Internet Gateway의 별칭

#### 6. _AWS Internet Gateway VPC 연결_

1. “AWS관리” 메뉴에서 “Internet Gateway” 메뉴를 클릭한다.
2. AWS Internet Gateway 목록에서 Internet Gateway를 선택한다\(Internet Gateway가 없다면 5항목의 AWS Internet Gateway 등록 참조\).
3. “VPC 연결” 버튼을 클릭한다.
4. AWS VPC 연결 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/vpc_connection.png)

※ VPC 연결 등록 정보

* VPC: Internet Gateway를 연결할 VPC 정보\(여기서는 2 항목에서 생성한 VPC\)

#### 7. _AWS Security Group 생성_

1. “AWS 관리” 메뉴에서 “Security Group” 관리 메뉴를 클릭한다.
2. AWS Security Group 생성 팝업 화면에서 정보를 입력한다.
   * BOOTSTRAP 설치 시 Ingress Rule 항목에 “bosh-security” 버튼을 클릭한다.
   * CF 설치 시 Ingress Rule 항목에 “cf-security” 버튼을 클릭한다.
   * Ingress Rule 항목에 “없음” 버튼을 클릭하고 Security Group 생성 시 모든 Inbound 포트가 열려 있다.

![](../../.gitbook/assets/security_group_add.png)

※ Security Group 등록 정보

* Security Group Tag: Security Group의 별칭
* Group Name: Security Group의 이름
* Description: Security Group에 대한 추가 정보
* VPC: Security Group이 적용될 VPC 정보\(여기서는 2 항목에서 생성한 VPC\)
* Ingress Rule: Security Group의 적용되는 진입 규칙

#### 8. _AWS Elastic IP 할당_

1. “AWS 관리” 메뉴에서 “Elastic IPs” 메뉴를 클릭한다.
2. “할당” 버튼을 클릭한다.
3. “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/elasticip_add.png)

#### 9. _AWS Key Pair 생성_

1. “AWS 관리” 메뉴에서 “Key Pair” 메뉴를 클릭한다.
2. “할당” 버튼을 클릭한다.
3. AWS KeyPair 생성 팝업에서 정보를 입력한 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/key_pair_add.png)

※ AWS Key Pair 등록 정보

* Key pair name: Key Pair 이름 설정 정보

#### 10. _AWS NAT Gateway 생성_

1. “AWS 관리” 메뉴에서 “NAT Gateway” 메뉴를 클릭한다.
2. “할당” 버튼을 클릭한다.
3. AWS NAT Gateway 생성 팝업에서 정보를 입력한 후 “확인” 버튼을 클릭한다.
4. NAT Gateway는 Elastic IP를 사용한 Public 서브넷\(3 항목에서 BOSH를 위해 생성한 서브넷\)에 연결한다.

**할당될 Elastic IP가 존재하지 않는다면, “NEW EIP 할당” 버튼을 클릭하여 Elastic IP를 생성 후 Elastic IP Allocation ID에 등록할 수 있다.**

![](../../.gitbook/assets/nat_gateway_add.png)

※ NAT Gateway 등록 정보

* Subnet: NAT Gateway에 할당될 서브넷 정보
* Elastic IP Allocation ID: NAT Gateway에 할당될 Elastic IP 정보

#### 11. _AWS Route Table 등록_

1. “AWS 관리” 메뉴에서 “Route Table” 메뉴를 클릭한다.
2. “라우트 테이블 생성” 버튼을 클릭한다.
3. Route Table 생성 팝업 화면에서 Route Table 정보를 입력한 후 “확인” 버튼을 클릭한다.
4. AWS Route는 Main/Main이 아닌 총 2개의 Router Table이 필요하다. 한 개는 VPC 생성 시 자동으로 생성된다.

![](../../.gitbook/assets/route_table_add.png)

※ Route Table설정 정보

* Name Tag: Route Table의 별칭
* VPC: Route Table의 기준이 되는 VPC 정보\(여기서는 2 항목에서 생성한 VPC\)

#### 12. _AWS Route 등록_

1. “AWS 관리” 메뉴에서 “Route Table” 메뉴를 클릭한다.
2. Route Table 목록에서 Route Table을 선택한다\(Route Table이 없다면 11항목의 AWS Route Table 등록 참조\).
3. “Route 추가” 버튼을 클릭한다.
4. Route 추가 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/route_add.png)

※ Route 등록 설정 정보

* Main Route 입력 정보 Destination 0.0.0.0/0, nats gateway를 target으로 설정한다.
* Main이 아닌 Route 입력 정보 Destination 0.0.0.0/0, internet gateway를 target으로 설정한다

#### 13. _AWS Associated Subnet 등록_

1. “AWS 관리” 메뉴에서 “Route Table” 메뉴를 클릭한다.
2. Route Table 목록에서 Route Table을 선택한다\(Route Table이 없다면 11항목의 AWS Route Table 등록 참조\).
3. Associated Subnets 목록에서 “Associate 하기” 버튼을 클릭한다.
4. Subnet Associate 팝업 화면에서 정보를 입력 후 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/subnet_associate.png)

※ Associated Subnet 등록 설정 정보

* Subnet Id: 서브넷 설정에서 등록한 서브넷의 ID
* IPv4 CIDR: 서브넷 설정에서 등록한 서브넷의 IPv4의 네트워크 범위\(자동입력\)
* IPv6 CIDR: 서브넷 설정에서 등록한 서브넷의 IPv4의 네트워크 범위\(자동입력\)

**※ Subnet 연결 시 Nat Gateway가 연결되어 있는 Main Router Table에는 Private Subnet에, Internet Gateway가 연결되어 있는 Main이 아닌 Router Table에는 Public Subnet을 연결한다.** **※ Elastic IP가 할당되는 서브넷은 Internet Gateway가 붙은 Main이 아닌 Route Table에, Elastic IP가 할당되지 않는 서브넷은 Nat Gateway가 붙은 Main Route Table에 연결해야 한다.**

### 2.4  스템셀과 릴리즈

플랫폼 설치 자동화를 통해 배포 가능한 BOOTSTRAP 버전은 아래와 같으며, 아래의 지원 가능한 버전을 이용하지 않을 경우 에러가 발생할 수 있다. 따라서 아래의 릴리즈 버전으로 다운로드 및 설치한다.

| BOSH 릴리즈 | CPI 릴리즈 | BPM | 스템셀 |
| :--- | :--- | :--- | :--- |
| bosh/267.8.0 | bosh-aws-cpi/72 | bpm/0.9.0 | bosh-aws-xen-hvm-ubuntu-trusty-go\_agent/3586.24 |
| bosh/268.2.0 | bosh-aws-cpi/72 | bpm/0.12.3 | bosh-aws-xen-hvm-ubuntu- xenial-go\_agent/97.28 |
| bosh/270.2.0 | bosh-aws-cpi/75 | bpm/1.1.0 | bosh-aws-xen-hvm-ubuntu- xenial-go\_agent/315.64 |

플랫폼 설치 자동화를 통해 배포 가능한 CF-Deployment 버전은 아래와 같으며, 아래의 릴리즈 버전으로 다운로드&업로드 및 설치한다.

| 릴리즈 버전 | 스템셀 |
| :--- | :--- |
| cf-deployment/2.7.0 | bosh-aws-xen-hvm-ubuntu-trusty-go\_agent/3586.25 |
| cf-deployment/3.2.0 | bosh-aws-xen-hvm-ubuntu-trusty-go\_agent/3586.27 |
| cf-deployment/4.0.0 | bosh-aws-xen-hvm-ubuntu-trusty-go\_agent/3586.40 |
| cf-deployment/5.0.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/97.18 |
| cf-deployment/5.5.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/97.28 |
| cf-deployment/9.3.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/315.36 |
| cf-deployment/9.5.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/315.64 |
| paasta/4.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/97.28 |
| paasta/4.6 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/315.36 |
| paasta/5.0 | bosh-aws-xen-hvm-ubuntu-xenial-go\_agent/315.64 |

### 2.5  BOOTSTRAP 설치하기

플랫폼 설치 자동화를 이용하여 인프라 환경 설정 및 BOOTSTRAP을 설치하고, 디렉터로 등록하는 절차는 다음과 같다.

![](../../.gitbook/assets/bootstrap_install_flow.png)

#### 2.5.1. _인프라 환경 설정 관리_

BOOTSTRAP을 설치하기 위해서는 설치할 인프라의 환경 설정 정보를 등록해야 한다. PaaS-TA를 설치하기 위해 “PaaS-TA 설치 자동화” 메뉴를 클릭하여 플랫폼 설치 자동화 대시보드화면으로 이동한다. 플랫폼 설치 자동화 웹 화면에서 “환경 설정 및 관리” -&gt; “인프라 환경 설정 관리” 메뉴로 이동한다. “인프라 환경 설정 관리” 메뉴에서는 AWS/OPENSTACK/vSphere/GOOGLE/Azure 등 5개 인프라의 전체 환경 설정 목록 조회 기능과 관리 화면으로 이동하는 기능을 제공한다.

![](../../.gitbook/assets/infra_add.png)

환경 설정 관리 화면 이동 컨테이너를 클릭하여 플랫폼 설치에 필요한 각 계정 정보를 등록한다.

**1. AWS 환경 설정 등록**

1. AWS 환경 설정 관리 화면에서는 “등록” 버튼을 클릭한다.
2. AWS 환경 설정 등록 팝업 화면에서 플랫폼 설치에 필요한 인프라 환경 설정 정보를 입력하고 “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/aws_infra_add.png)

※ AWS 환경 설정 등록 정보

* AWS 환경 설정 별칭: 등록할 AWS 환경 설정 정보의 별칭
* AWS 계정 별칭: 등록된 AWS 계정 정보의 별칭
* Security Group: 인스턴스에 허용되는 인바운드 네트워크 연결의 명명된 집합
* Region: 동일한 지리적 영역에 있는 명명된 AWS 리소스 집합
* Availability Zone: Region 내의 개별적인 지점 정보
* Keypair Name: Keypair 명
* Private Key File: 개인 키 파일 업로드 정보\(Keypair의 Private Key\)

#### 2.5.2. _스템셀 다운로드_

플랫폼 설치 자동화 웹 화면에서 “환경설정 및 관리” -&gt; “스템셀 관리” 메뉴로 이동한다. “스템셀 관리” 메뉴에서는 Cloud Foundry에서 제공하는 공개 스템셀을 다운로드할 수 있는 기능을 제공한다. 스템셀 다운로드 유형은 총 3가지이며 Version유형으로 다운로드가 안될 경우 로컬에서 다운로드 후 로컬에서 선택 유형/스템셀 다운로드 URL을 통해 다운로드 받는 유형을 이용한다. 상단에 위치한 “등록” 버튼을 클릭 후 스템셀 정보를 입력하고 “확인” 버튼을 클릭한다.

```text
https://bosh.io/stemcells/bosh-aws-xen-hvm-ubuntu-xenial-go_agent
```

**※ ※ 본 가이드에서는 버전 Xenial 315.64를 다운로드 하였다.**

![](../../.gitbook/assets/stemcell_add%20%282%29.png)

#### 2.5.3. _릴리즈 다운로드_

BOOTSTRAP을 설치하기 위해서는 BOSH 릴리즈와 BOSH CPI릴리즈 2개의 릴리즈가 필요하며, BOSH 릴리즈 버전 266 이상일 경우 BPM 릴리즈가 더 추가되어 총 3개의 릴리즈가 필요하다. 릴리즈 다운로드 유형은 총 3가지이며 Version유형으로 다운로드가 안될 경우 로컬에서 다운로드 후 로컬에서 선택 유형/릴리즈 다운로드 URL을 통해 다운로드 받는 유형을 이용한다. 릴리즈를 다운로드하기 위해 플랫폼 설치 자동화 웹 화면에서 “환경설정 및 관리” -&gt; “릴리즈 관리” 메뉴로 이동 후 상단에 위치한 “등록” 버튼을 클릭하고, 릴리즈 등록 팝업 화면에서 릴리즈 정보 입력 후 “등록” 버튼을 클릭한다.

**1. BOSH 릴리즈**

1. 릴리즈 등록 팝업 화면에서BOSH 릴리즈 정보를 입력하고, “등록” 버튼 클릭한다.
2. BOSH 릴리즈 참조 사이트

   [http://bosh.io/releases/github.com/cloudfoundry/bosh?all=1](http://bosh.io/releases/github.com/cloudfoundry/bosh?all=1)

![](../../.gitbook/assets/bosh_release_add.png)

**본 가이드에서는 v270.2.0을 다운로드 하였다.**

**2. BOSH CPI 릴리즈**

1. 릴리즈 등록 팝업화면에서 BOSH CPI릴리즈 정보를 입력하고, “등록” 버튼 클릭한다.
2. BOSH-CPI 릴리즈 참조 사이트

   [https://bosh.io/releases/github.com/cloudfoundry-incubator/bosh-aws-cpi-release?all=1](https://bosh.io/releases/github.com/cloudfoundry-incubator/bosh-aws-cpi-release?all=1)

![](../../.gitbook/assets/cpi_release_add.png)

**본 가이드에서는 v75을 다운로드 하였다.**

**3. BPM 릴리즈**

1. 릴리즈 등록 팝업화면에서 BPM릴리즈 정보를 입력하고, “등록” 버튼 클릭한다.
2. BPM 릴리즈 참조 사이트

   [https://bosh.io/releases/github.com/cloudfoundry-incubator/bpm-release?all=1](https://bosh.io/releases/github.com/cloudfoundry-incubator/bpm-release?all=1)

![](../../.gitbook/assets/bpm_release_add.png)

**본 가이드에서는 v1.1.0을 다운로드 하였다.**

**4. OS CONF 릴리즈**

1. 릴리즈 등록 팝업화면에서 OS CONF 릴리즈 정보를 입력하고, “등록” 버튼 클릭한다.
2. OS-CON 릴리즈 참조 사이트

   [https://bosh.io/releases/github.com/cloudfoundry/os-conf-release?all=1](https://bosh.io/releases/github.com/cloudfoundry/os-conf-release?all=1)

![](../../.gitbook/assets/os_release_add%20%284%29%20%284%29.png)

**본 가이드에서는 v21.0.0을 다운로드 하였다.**

#### 2.5.4. _디렉터 인증서 생성_

BOOTSTRAP을 설치하기 위해서는 Nats/Director 컴포넌트를 사용하기 위한 인증서 정보, 디렉터 인증서가 필요하며 디렉터 인증서를 생성하기 위해 플랫폼 설치 자동화 웹 화면에서 “환경설정 및 관리” -&gt; “디렉터 인증서 관리” 메뉴로 이동 후 상단에 위치한 “등록” 버튼을 클릭하고, 디렉터 인증서 팝업 화면에서 디렉터 인증서 정보 입력 후 “등록” 버튼을 클릭한다.

![](../../.gitbook/assets/director_credential_add%20%281%29%20%281%29%20%281%29.png)

※ 디렉터 인증서 등록 정보

* 디렉터 인증서 명: 디렉터 인증서 정보의 별칭
* BOOTSTRAP 공인 IPs: BOOTSTRAP이 설치될 VM의 공인 IP 정보\(AWS 인프라 관리에서 Elastic IP 설정을 통해 생성된 IP를 사용\)
* BOOTSTRAP 내부 IPs: BOOTSTRAP이 설치될 VM의 내부 IP 정보
* BOOTSTRAP 공인 IPs 입력항목은 사용자의 설정에 따라 사용시 값을 입력할 수 있고, 사용하지 않을 경우 이를 제외한 항목만 채워 넣어도 디렉터 인증서 생성이 가능하다

#### 2.5.5. _BOOTSTRAP 설치_

BOOTSTRAP 설치하기 위해 플랫폼 설치 자동화 웹 화면에서 “플랫폼 설치” -&gt; “BOOTSTRAP 설치” 메뉴로 이동 후 상단에 위치한 “설치” 버튼을 클릭한다.

**1. 클라우드 환경 선택**

1. 설치할 클라우드 환경을 선택하는 팝업화면에서 AWS를 선택한다.
2. “확인” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_selectiaas.png)

**2. BOOTSTRAP 설치 – 선택한 클라우드 환경 정보**

1. AWS 클라우드 환경을 선택한 경우 인프라 환경 설정 관리에서 설정한 AWS 인프라 환경 별칭을 선택 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_aws.png)

※ BOOTSTRAP AWS 등록 정보

* BOOTSTRAP AWS 등록 정보의 경우 인프라 환경 설정 관리에서 설정한 정보와 동일한 정보가 들어간다.\(2.3.1 인프라 환경 설정 관리 참조\)

**3. BOOTSTRAP 설치 – 기본 정보**

1. AWS 환경을 선택한 경우 아래의 기본 정보 입력 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_default.png)

※ BOOTSTRAP 기본 정보 등록 정보

* 배포 명: BOOTSTRAP 배포 시 사용하는 명칭
* 디렉터 명: 디렉터 명칭
* 디렉터 접속 인증서: 디렉터 설정에서 등록한 디렉터 인증서 정보
* NTP: 신뢰하고 정확한 시간 원본과의 통신을 통하여 호스트 또는 노드를 위해 시계를 유지하는 방식으로 공인된 주소를 사용
* BOSH 릴리즈: 릴리즈 관리에서 등록한 BOSH 릴리즈 정보
* BOSH CPI 릴리즈: 릴리즈 관리에서 등록한 BOSH CPI 릴리즈 정보
* OS-CONF 릴리즈: 설치할 OS-CONF 릴리즈를 선택
* BOSH-BPM 릴리즈: 설치할 BPM 릴리즈 선택, 특정 BOSH 버전 이상일 경우 사용
* 스냅샷기능 사용여부: 스토리지 볼륨 또는
* PaaS-TA 모니터링 정보: PaaS-TA 모니터링을 이용하려면 BOSH Release v268.2.0를 선택하고 PaaS-TA 모니터링 사용을 선택한다.이미지에 대한 특정 시점에서의 사본. 볼륨을 백업하기 위해 스냅샷 기능 사용유무 체크

**BOOTSTRAP 릴리즈 Name Tag의 “?” 아이콘을 통해 현재 플랫폼 설치 자동화에서 설치 가능한 BOSH의 버전을 확인한다.**

**4. BOOTSTRAP 설치 – 클라우드 환경 별 네트워크 정보**

1. AWS 환경을 선택한 경우 아래의 네트워크 정보 입력 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_network.png)

※ BOOTSTRAP 네트워크 등록 정보

* 디렉터 공인 IP: BOOTSTRAP이 설치될 VM의 공인 IP 정보, 공인 IP를 사용하지 않을 경우 값을 입력하지 않는다
* 디렉터 내부 IP: BOOTSTRAP이 설치될 VM의 내부IP 정보
* 서브넷 아이디: 인프라 관리에서 생성한 AWS VPC의 서브넷 ID
* 서브넷 범위: 인프라 관리에서 생성한 AWS 서브넷의 네트워크 범위
* 게이트웨이: 인프라 관리에서 생성한 AWS 서브넷의 게이트웨이 주소
* DNS: 인터넷 도메인에 대한 이름-주소 및 주소-이름 입력

**5. BOOTSTRAP 설치 – 리소스 정보**

1. AWS 환경을 선택한 아래의 리소스 정보 입력 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_resource_4.6.png)

※ BOOTSTRAP 리소스 등록 정보

* 스템셀: BOOTSTRAP이 설치될 VM의 스템셀 정보
* 인스턴스 유형: BOOTSTRAP이 설치될 VM의 인스턴스 유형 정보

**7. BOOTSTRAP 설치 – 설치**

1. 생성된 배포 Manifest파일 정보를 이용하여 BOOTSTRAP설치를 실행하고 설치 진행 과정에 대한 로그를 확인한다
2. 설치가 완료되면 “닫기” 버튼을 클릭한다.

![](../../.gitbook/assets/bootstrap_install.png)

#### 2.5.6 _디렉터 설정_

BOOTSTRAP설치가 완료되면 BOOTSTRAP 디렉터 정보를 이용해서 플랫폼 설치 자동화의 디렉터로 설정한다. 디렉터를 등록 위해서는 플랫폼 설치 자동화 웹 화면에서 “환경설정 및 관리” -&gt; “디렉터 설정” 메뉴로 이동 후 상단에 위치한 “등록” 버튼을 클릭하고, 디렉터 등록 팝업 화면에서 디렉터 정보 입력 후 “등록” 버튼을 클릭한다. 이미 디렉터가 존재할 경우 디렉터를 선택하고 “기본 디렉터로 설정” 버튼을 클릭한다.

계정 및 비밀번호, 포트번호는 “admin/admin/25555”이다.

![](../../.gitbook/assets/director_add%20%283%29%20%282%29%20%281%29.png)

※ 디렉터 설정 등록 정보

* 디렉터 IP: BOOTSTRAP 설치 AWS IP 정보를 입력
* 포트번호: BOOTSTRAP 설치 Manifest의 Director Port 번호 입력\(default 25555\)
* 계정: BOOTSTRAP 설치 Manifest의 user\_management 아래 Director User 입력
* 비밀번호: BOOTSTRAP 설치 Manifest의 user\_management아래 Director Password 입력

### 2.6. _CF-Deployment 설치하기_

BOSH를 설치하고 플랫폼 설치 자동화의 디렉터로 설정이 완료되면 CF-Deployment를 설치할 준비가 된 상태로 PaaS-TA를 설치하는 절차는 다음과 같다.

![](../../.gitbook/assets/cf_flow%20%284%29%20%282%29%20%281%29.png)

#### 2.6.1 _스템셀 업로드_

플랫폼 설치 자동화에서 다운받은 스템셀을 “스템셀 업로드” 화면을 통해 디렉터에 315.64 버전의 스템셀을 업로드 한다.

![](../../.gitbook/assets/stemcell_upload.png)

#### 2.6.2 _PaaS-TA 릴리즈 사용_

※ 해당 절차는 PaaS-TA를 설치하기 위해 반드시 필요한 Compiled Local 릴리즈를 다운로드하는 절차이다. PaaS-TA를 설치하기 위해 필요한 절차이다. ※ 플랫폼 설치 자동화를 통해 배포 가능한 PaaS-TA 버전에 맞는 릴리즈와 스템셀을 PaaS-TA 공식 홈페이지 [https://paas-ta.kr/download/package에서](https://paas-ta.kr/download/package에서) 다운로드 받는다.

1. PaaS-TA 릴리즈 사용

   1.1. 다운로드 한 paasta 릴리즈 압축 파일을 scp 명령어를 통해 플랫폼 설치 자동화가 동작하고 있는 Inception 서버로 이동시킨다.

   ex\) $ scp -i {inception.key} ubuntu@172.xxx.xxx.xx {릴리즈 압축 일 명} \# Key Pair를 사용할 경우 ex\) $ scp ubuntu@172.xxx.xxx.xx {릴리즈 압축 일 명} \# Password를 사용할 경우

   1.2. 릴리즈 디렉토리를 생성하고 릴리즈 디렉토리에서 해당 릴리즈 파일의 압축을 해제한다. 릴리즈 디렉토리의 위치는 반드시 {home}/workspace/paasta-5.0/release/paasta여야 한다.

   * 디렉토리 생성

     ex\) $ mkdir -p workspace/paasta-5.0/release/paasta

   * 릴리즈 압축 파일 이동

     ex\) $ mv {릴리즈 압축 파일 명} workspace/paasta-5.0/release/paasta/

   * 릴리즈 파일 압축 해제

     ex\) $ tar xvf {릴리즈 압축 파일 명} \# 릴리즈 파일 확장자가 tar인 경우

     ex\) $ unzip {릴리즈 압축 파일 명} \# 릴리즈 파일 확장자가 zip인 경우

     1.3.    아래는 릴리즈 디렉토리의 PaaS-TA 릴리즈 형상 예시 그림이다.

![](../../.gitbook/assets/hb_cfdeployment_paasta_release_5.0%20%281%29%20%286%29%20%286%29.png)

#### 2.6.3 _CF-Deployment 설치_

CF-Deployment를 설치하기 위해 플랫폼 설치 자동화 웹 화면에서 “플랫폼 설치” -&gt; “CF-Deployment설치” 메뉴로 이동 후 상단의 “설치” 버튼을 클릭한다.

**1.    CF-Deployment설치 – 기본 정보 등록**

1. 배포에 필요한 기본정보와 도메인 / 로그인 비밀번호를 입력 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/cf_default_version%20%284%29%20%281%29%20%282%29.png)

**본 가이드에서는 CF-Deployement 버전으로 Paasta 5.0을 사용하였다.**

※ CF-Deployment 기본 등록 정보

* 디렉터 UUID: 기본 디렉터의 UUID \(자동 입력\)
* 배포 명: CF-Deployment 설치 배포 명 입력
* CF-Deployment 버전: 플랫폼 설치 자동화에서 지원하는 CF Deployment 버전 선택
* CF Database 유형: CF Database 컴포넌트의 유형, Mysql로 설치 시 컴파일 시간이 오래 걸릴 수 있음
* Inception User Name: Inception 서버의 계정 명 ex\) vcap
* CF Admin Password: CF Login 패스워드 입력
* 도메인: CF 설치에 사용 할 도메인 입력 ex\) {public IP}.xip.io
* Portal 도메인: Portal을 설치 및 접속할 도메인 주소를 입력한다. Portal을 설치하지 않고 CF-Deployment를 실행할 경우 해당 값을 입력하지 않는다.
* PaaS-TA 모니터링 정보: PaaS-TA 모니터링을 이용하려면 paasta/5.0을 선택하고 PaaS-TA 모니터링 사용을 선택한다.

**2.    CF-Deployment설치 – 네트워크 정보 등록**

1. AWS 환경일 경우 AWS의 네트워크 정보 입력 후 “다음” 버튼을 클릭한다.
2. “추가” 버튼을 클릭하여 네트워크를 추가하여 AZ를 분리 배치할 수 있다.

![](../../.gitbook/assets/cf_network.png)

※ CF-Deployment 네트워크 등록 정보

* CF API TARGET IP: CF Deployment 설치 시 사용되는 AWS Public IP
* 서브넷 아이디: 인프라 관리에서 생성한 AWS VPC의 서브넷 명
* 보안 그룹: 인프라 관리에서 생성한 AWS 서브넷의 보안 그룹 명
* 가용 영역: 인프라 관리에서 설정한 AWS Region 내의 개별적인 지점
* 서브넷 범위: 인프라 관리에서 생성한 AWS 서브넷의 네트워크 범위
* 게이트웨이: 인프라 관리에서 생성한 AWS 서브넷의 게이트웨이 주소
* DNS: DNS 서버 주소
* IP 할당 제외 대역: CF Deployment VM을 배치하지 않을 IP 주소 시작/끝 입력
* IP 할당 대역\(최소 20개\): CF Deployment VM을 배치할 IP 주소 시작/끝 입력

**AWS의 경우 복수개의 Internal\(서브넷\) 네트워크를 사용해야만 한다. 이는 AWS에는 Public/Private 용도의 서브넷이 존재하고 diego-cell\(컨테이너\)을 Private 서브넷으로 구분하기 위함이다.**

**3.    CF-Deployment설치 – Key 생성 및 정보 등록**

1. Key 생성 정보 입력 후 “Key 생성” 버튼을 클릭한다.
2. Key 생성 확인 후 “다음” 버튼을 클릭한다.

![](../../.gitbook/assets/cf_key.png)

※ CF-Deployment Key 생성 등록 정보

* 도메인: CF Deployment 도메인 주소 \(자동 입력\)
* 국가 코드: 국가 코드 선택
* 시/도: 시/도 입력
* 시/구/군: 시/구/군 입력
* 회사명: 회사명 입력
* 부서명: 부서 명 입력
* Email: 이메일 주소 입력

**4.    CF-Deployment설치 – 리소스 정보 등록**

1. AWS 환경일 경우 아래의 정보를 입력 후 “다음” 버튼을 클릭한다.

※ CF-Deployment Key 생성 등록 정보

* Stemcell: 기본 디렉터에 업로드 한 스템셀 선택
* Small Resource Type: AWS 환경의 Small Instance Type
* Medium Resource Type: AWS 환경의 Medium Instance Type
* Large Resource Type: AWS환경의 Large Instance Type

![](../../.gitbook/assets/cf_resource.png)

**5.    CF-Deployment설치 – 인스턴스 정보 등록**

1. AWS 환경일 경우 아래의 정보를 입력 후 “다음” 버튼을 클릭한다.
2. 인스턴스 수가 늘어나게 되면 해당 수만큼 네트워크 대역이 필요해 네트워크 할당 대역을 늘려줄 필요 가 있다.

![](../../.gitbook/assets/cf_instance.png)

※ CF-Deployment Key 생성 등록 정보

* 인스턴스 수: VM에 할당할 인스턴스 수

**6.    CF-Deployment설치 – 설치**

1. 생성된 배포 Manifest파일 정보를 이용하여 CF-Deployment설치를 실행하고 설치 진행 과정에 대한 로그를 확인한다.

![](../../.gitbook/assets/cf_install%20%283%29%20%281%29.png)

### 2.7. _서비스팩 설치_

BOSH 및 CF-Deployment 설치가 성공적으로 완료되고 배포할 Manifest를 업로드하면 서비스팩을 설치할 준비가 된 상태로 서비스팩을 설치하는 절차는 다음과 같다.

![](../../.gitbook/assets/servicepack_flow%20%284%29%20%282%29%20%281%29.png)

#### 2.7.1. _릴리즈 업로드_

PaaS-TA개발팀에서 제공하는 PaaS-TA 서비스 릴리즈에서 “릴리즈 다운로드”를 통해 다운 받는다. 그리고 “릴리즈 업로드”와 동일하게 디렉터로 업로드한다.

#### 2.7.2. _Manifest 업로드_

Manifest를 업로드 하기 위해 플랫폼 설치 자동화 웹 화면에서 “배포 정보 조회 및 관리” -&gt; “Manifest 관리” 메뉴로 이동 후 상단의 “업로드” 버튼을 클릭한다.

**1. Manifest 업로드 – 업로드**

1. 서비스팩 설치를 위해서는 배포 정보를 가지고 있는 Manifest 파일이 필요하다. 서비스팩 설치에 필요한 Manifest를 작성하여 플랫폼 설치 자동화에 업로드 한다.

![](../../.gitbook/assets/manifest_upload%20%283%29%20%281%29%20%282%29.png)

**본 가이드에서는 PaaS-TA 서비스 influxdb-grafana Manifest를 업로드 하였다.** **업로드 할 Manifest는 모든 정보가 입력되어 있는 Pull Manifest여야 한다.**

#### 2.7.3. _서비스팩 설치_

서비스팩을 설치하기 위해 플랫폼 설치 자동화 웹 화면에서 “플랫폼 설치” -&gt; “서비스팩 설치” 메뉴로 이동 후 상단의 “설치” 버튼을 클릭한다.

**1.    서비스팩 설치 – Manifest 등록**

1. 배포에 필요한 Manifest 파일을 선택하고 “설치” 버튼을 클릭 한다

![](../../.gitbook/assets/manifest_add%20%284%29%20%284%29%20%283%29.png)

**2.    서비스팩 설치 – 설치**

1. 생성된 배포 Manifest파일 정보를 이용하여 서비스팩 설치를 실행하고 설치 진행 과정에 대한 로그를 확인한다.

![](../../.gitbook/assets/servicepack_install%20%283%29%20%283%29%20%283%29.png)

