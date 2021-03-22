# PAAS-TA\_PLATFORM\_INSTALL\_AUTOMATION\_CLOUDIT\_v1.0

## Table of Contents

1. [문서 개요](paas-ta_platform_install_automation_cloudit_v1.0.md#1)
   * [목적](paas-ta_platform_install_automation_cloudit_v1.0.md#2)
   * [범위](paas-ta_platform_install_automation_cloudit_v1.0.md#3)
2. [플랫폼 설치 가이드](paas-ta_platform_install_automation_cloudit_v1.0.md#4)
   * [인프라 설정](paas-ta_platform_install_automation_cloudit_v1.0.md#5)
   * [스템셀과 릴리즈](paas-ta_platform_install_automation_cloudit_v1.0.md#6)
     * [BOOTSTRAP](paas-ta_platform_install_automation_cloudit_v1.0.md#7)
     * [CF 스템셀](paas-ta_platform_install_automation_cloudit_v1.0.md#8)
   * [BOOTSTRAP 설치하기](paas-ta_platform_install_automation_cloudit_v1.0.md#9)
     * [스템셀 다운로드](paas-ta_platform_install_automation_cloudit_v1.0.md#10)
     * [릴리즈 다운로드](paas-ta_platform_install_automation_cloudit_v1.0.md#11)
     * [BOOTSTRAP 설치 전 사전 작업](paas-ta_platform_install_automation_cloudit_v1.0.md#12)
     * [BOOTSTRAP 설치](paas-ta_platform_install_automation_cloudit_v1.0.md#13)
   * [CF-Deployment 설치하기](paas-ta_platform_install_automation_cloudit_v1.0.md#14)
     * [스템셀 업로드](paas-ta_platform_install_automation_cloudit_v1.0.md#15)
     * [Cloud-Config와 Runtime-Config 설정](paas-ta_platform_install_automation_cloudit_v1.0.md#16)
     * [CF-Deployment 설치](paas-ta_platform_install_automation_cloudit_v1.0.md#17)
     * [CF App push 테스트](paas-ta_platform_install_automation_cloudit_v1.0.md#18)
     * [별첨 : DCE 클러스터 연동](paas-ta_platform_install_automation_cloudit_v1.0.md#19)

## Deprecated

**※ 본 문서는 PaaS-TA 5.0.1 이하의 버전까지 지원한다.**

## 1.  문서 개요

### 1.1.  목적

본 문서는 CLI 기반으로 PaaS-TA 구축하는 절차에 대해 기술하였다.

### 1.2.  범위

본 문서에서는 Cloudit 인프라 환경에서 Kubernetes 기반의 PaaS-TA를 설치하는 방법에 대해 작성되었다.

## 2.  플랫폼 설치 가이드

BOSH는 클라우드 환경에 서비스를 배포하고 소프트웨어 릴리즈를 관리해주는 오픈 소스로 Bootstrap은 하나의 VM에 설치 관리자의 모든 컴포넌트를 설치한 것으로 PaaS-TA 설치를 위한 관리자 기능을 담당한다. Cloudit 환경의 Bootstrap은 물리적인 하나의 VM이 아닌 컨테이너 기반으로 동작한다.

Cloudit 클라우드 환경에 PaaS-TA를 설치하기 위해서는 인프라 설정, 스템셀 소프트웨어 릴리즈, Manifest 파일, 인증서 파일 5가지 요소가 필요하다. 스템셀은 클라우드 환경에 VM을 생성하기 위해 사용할 기본 이미지이고, 소프트웨어 릴리즈는 VM에 설치할 소프트웨어 패키지들을 묶어 놓은 파일이고, Manifest파일은 스템셀과 소프트웨어 릴리즈를 이용해서 서비스를 어떤 식으로 구성할지를 정의해 놓은 명세서이다. 다음 그림은 BOOTSTRAP을 이용하여 PaaS-TA를 설치하는 절차이다.

![](../../.gitbook/assets/install_flow%20%282%29.png)

### 2.1  인프라 설정

**1. Cloudit 리소스 관리**

플랫폼 디렉터의 코드/권한/사용자 관리를 통해 전체 사용자의 생성 및 사용 권한과 공통 Manifest 버전/신규 스템셀 확장 등의 공통 코드를 사용할 수 있다.

| 인프라 환경 | 메뉴 |  |  |  |  |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 컴퓨트 | Inception VM 생성 | 도커 | 도커 클러스터 생성 | 네트워크 | 로드밸런서 생성 |

**2. Cloudit Inception VM 생성 - 컴퓨트**

2.1. Cloudit 포탈에 접속한다. 접속 주소 : [https://www.cloudit.co.kr](paas-ta_platform_install_automation_cloudit_v1.0.md)

2.2. 컴퓨트 메뉴를 클릭한다.

2.3. 컴퓨트 화면에서 “Create” 버튼을 클릭한다. ![](../../.gitbook/assets/inception_list_server.png)

2.3.1. 컴퓨트 생성 팝업에서 Ubuntu OS를 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/inception_choice_os.png)

2.3.2. 구성할 Inception VM의 사양을 선택한다. ![](../../.gitbook/assets/inception_choice_spec.png)

2.3.3. 구성할 Inception VM에 적용시킬 보안그룹을 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/inception_choice_securitygroup.png)

2.3.4. 구성할 Inception VM의 클러스터 설정을 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/inception_choice_clustertype.png)

2.3.5. 구성할 Inception VM의 서버 정보를 입력 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/inception_choice_vminfo.png)

※ 이름 : 생성할 Inception VM의 이름 호스트명 : 생성할 Inception의 내부 Host 이름 루트 비밀번호 : Inception의 root 계정 비밀번호

2.3.6. 구성할 Inception VM의 최종 정보를 확인 후 확인 버튼을 클릭한다. ![](../../.gitbook/assets/inception_verify_finalinfo.png)

**3. Cloudit 도커 클러스터 생성 - 도커**

3.1. Cloudit 포탈에 접속한다. 접속 주소: [https://www.cloudit.co.kr](https://www.cloudit.co.kr)

3.2. 도커 메뉴를 클릭한다.

3.3. 도커 화면에서 “Create” 버튼을 클릭한다. ![](../../.gitbook/assets/docker_list_cluster.png)

3.3.1. 클러스터 생성 팝업에서 Master Node의 사양을 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/docker_choice_master_spec.png)

3.3.2. 구성할 도커 클러스터의 Worker Node 사양을 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/docker_choice_worker_spec.png)

3.3.3. 구성할 도커 클러스터에서 구성할 기본 보안 그룹 이외 추가로 오픈 시킬 보안 그룹을 선택 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/docker_choice_security_group.png)

3.3.4. 구성할 도커 클러스터의 정보를 입력 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/docker_choice_vminfo.png) ※ 이름 : 클러스터의 이름 호스트명 : 클러스터의 Master Node 호스트명. Worker Node는 해당 호스트 명 뒤에 ‘-\#’ 이 추가됨\(ex. cluster, cluster-1, cluster-2, …\) 루트 비밀번호 : 클러스터 서버 비밀번호\(Master, Worker Node 공통\)

3.3.5 구성할 도커 클러스터의 최종 정보를 확인 후 확인 버튼을 클릭한다. ![](../../.gitbook/assets/docker_verify_finalinfo.png)

**4. Cloudit 로드밸런서 생성 – Micro-Bosh VM 연결\(BOOTSTRAP 진행 도중 진행\) – 네트워크**

4.1. 로드밸런서 생성 중 Micro-Bosh VM을 멤버로 등록시엔 다음과 같은 포트로 구성한다 ※ 서버 포트는 재정의 가능

| 대상 | 용도 | LB 포트 | 서버 포트\(예\) | 비고 |
| :--- | :--- | :--- | :--- | :--- |
|  Micro-Bosh가 배포된 VM | Director | 25555 | 31274 |  |
| Blobstore | 25250 | 30482 |  |  |
| Nats | 4222 | 30428 |  |  |
| Agent | 6868 | 32614 |  |  |

4.2. Cloudit 포탈에 접속한다. 접속 주소: [https://www.cloudit.co.kr/](paas-ta_platform_install_automation_cloudit_v1.0.md)

4.3. 네트워크 메뉴를 클릭한다.

4.4. 로드밸런싱 메뉴를 클릭한다.

4.5. 로드밸런싱 화면에서 “Create” 버튼을 클릭한다. ![](../../.gitbook/assets/lb_bosh_list_lb%20%282%29.png)

4.5.1. 로드밸런싱 생성 팝업에서 기본 정책을 입력 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_bosh_choice_basicpolicy.png)

※ 이름 : 로드밸런서 이름 입력 포트 : 로드밸런서 Port 입력 \(상단 4.1의 [LB 포트 항목](paas-ta_platform_install_automation_cloudit_v1.0.md#22) 참조\)

유형 : 로드밸런서 IP 종류 선택 \(External : 로드밸런싱에서 이용할 Public VIP로 지정하여 사용, Internal : 로드밸런싱 서브넷에서 사용\)

IP : 로드밸런서의 IP 이며 동일 IP로 여러 개의 Port 설정 가능하다. 해당 샘플에서는 Micro-Bosh가 배포된 VM에 부여할 로드밸런싱 IP를 선택한다.

프로토콜 : TCP 또는 HTTP 프로토콜

정책 : 4가지 Load Balancing 정책 중 선택

|  **Round Robin** : 순차적으로 세션을 연결하는 정책, 거의 균등한 부하 분산이 가능하나 세션 유지 불가능  **Least Connection** : 세션 요구량이 적은 쪽으로 신규 세션을 연결해주는 정책  **Source Hash** : 출발지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 목적지로 세션을 연결해주는 정책  **Destination Hash** : 목적지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 출발지와 세션을 연결해주는 정책 |
| :--- |


4.5.2. 구성할 로드밸런서의 상태 체크 설정 및 멤버 등록 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_bosh_add_member.png)

※ 모니터 타입 : 상태 체크 프로토콜 선택 http 경로 : 모니터 타입이 HTTP인 경우 상태 체크 할 URL 입력

응답대기 시간 : 로드밸런싱 응답 대기 시간 입력

정상 판단 횟수 : 응답 대기 시간 동안 응답하지 않을 때 정상 판단 횟수

상태 확인 주기\(초\) : 로드밸런싱 상태 확인 주기

비정상 판단 횟수 : 상태 확인 주기동안 응답하지 않은 때 비정상 판단 횟수

서버 IP : 로드밸런싱 정책에 의해 작동 될 물리 VM의 IP선택

서버 포트 : 로드밸런싱 정책에 의해 작동 될 물리 VM의 Port 선택 \(상단 4.1 포트 구성표의 서버 포트 항목 참조\)

4.5.3. 구성할 로드밸런싱의 최종 정보를 확인 후 확인 버튼을 클릭한다 ![](../../.gitbook/assets/lb_bosh_verify_finalinfo.png)

**5. Cloudit 로드밸런서 생성 – Router/HAProxy배포 VM \(CF-Deployment 배포 이후 진행\) – 네트워크**

5.1. 로드밸런서 생성 중 Router 또는 HAProxy가 배포된 VM을 멤버로 등록시엔 다음과 같은 포트로 구성한다

| 대상 | 용도 | LB 포트 | 서버 포트\(예\) | 비고 |
| :--- | :--- | :--- | :--- | :--- |
|  CF의 Router 또는 HAProxy가 배포된 VM | HTTP | 80 | 30274 |  |
| HTTPS | 443 | 30732 |  |  |

5.2. Cloudit 포탈에 접속한다. 접속 주소: [https://www.cloudit.co.kr/](paas-ta_platform_install_automation_cloudit_v1.0.md)

5.3. 네트워크 메뉴를 클릭한다.

5.4. 로드밸런싱 메뉴를 클릭한다.

5.5. 로드밸런싱 화면에서 “Create” 버튼을 클릭한다. ![](../../.gitbook/assets/lb_bosh_list_lb%20%282%29%20%281%29.png)

5.5.1. 로드밸런싱 생성 팝업에서 기본 정책을 입력 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_cf_choice_basicpolicy.png)

※ 이름 : 로드밸런서 이름 입력 포트 : 로드밸런서 Port 입력 \(상단 5.1 포트 구성표의 [LB 포트 항목](paas-ta_platform_install_automation_cloudit_v1.0.md#23) 참조\)

유형 : 로드밸런서 IP 종류 선택 \(External : 로드밸런싱에서 이용할 Public VIP로 지정하여 사용 Internal : 로드밸런싱 서브넷에서 사용\)

IP : 로드밸런서의 IP 이며 동일 IP로 여러 개의 Port 설정 가능하다. 해당 샘플에서는 Router 또는 HAProxy가 배포된 VM에 부여할 로드밸런싱 IP를 선택한다.

프로토콜 : TCP 또는 HTTP 프로토콜

정책 : 4가지 Load Balancing 정책 중 선택

|  **Round Robin** : 순차적으로 세션을 연결하는 정책, 거의 균등한 부하 분산이 가능하나 세션 유지 불가능  **Least Connection** : 세션 요구량이 적은 쪽으로 신규 세션을 연결해주는 정책  **Source Hash** : 출발지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 목적지로 세션을 연결해주는 정책  **Destination Hash** : 목적지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 출발지와 세션을 연결해주는 정책 |
| :--- |


5.5.2. 구성할 로드밸런서의 상태 체크 설정 및 멤버 등록 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_cf_add_member.png)

※ 모니터 타입 : 상태 체크 프로토콜 선택 http 경로 : 모니터 타입이 HTTP인 경우 상태 체크 할 URL 입력

응답대기 시간 : 로드밸런싱 응답 대기 시간 입력

정상 판단 횟수 : 응답 대기 시간 동안 응답하지 않을 때 정상 판단 횟수

상태 확인 주기\(초\) : 로드밸런싱 상태 확인 주기

비정상 판단 횟수 : 상태 확인 주기동안 응답하지 않은 때 비정상 판단 횟수

서버 IP : 로드밸런싱 정책에 의해 작동 될 물리 VM의 IP선택.

서버 포트 : 로드밸런싱 정책에 의해 작동 될 물리 VM의 Port 선택 \(상단 5.1 포트 구성표의 서버 포트 항목 참조\)

5.5.3 구성할 로드밸런싱의 최종 정보를 확인 후 확인 버튼을 클릭한다. ![](../../.gitbook/assets/lb_cf_verify_finalinfo.png)

### 2.2.  스템셀과 릴리즈

#### 2.2.1. **BOOTSTRAP**

Cloudit의 Kubernetes 환경에 배포 가능한 BOOTSTRAP 버전은 아래와 같다. 아래의 릴리즈 버전으로 다운로드 및 설치한다. 아래의 버전을 사용하지 않을 경우 에러가 발생할 수 있다.

| BOSH 릴리즈 | CPI 릴리즈 | BPM | 스템셀 |
| :--- | :--- | :--- | :--- |
| bosh/269.0.1 | bosh-kubernetes-cpi/version | bpm/1.0.4 |  bosh-stemcell-315.22-warden-boshlite-ubuntu-xenial-go\_agent/315.22 |

#### 2.2.1. **CF 스템셀**

Cloudit의 Kubernetes 환경에 배포 가능한 CF-Deployment 버전은 아래와 같다. 아래의 릴리즈 버전으로 다운로드&업로드 및 설치한다.

| 릴리즈 | 스템셀 |
| :--- | :--- |
| paasta/4.0 |  bosh-cloudit-kvm-ubuntu-xenial-go\_agent/97.28 |

### 2.3.  **BOOTSTRAP 설치하기**

인프라 환경 설정 및 BOOTSTRAP을 설치하고, 디렉터로 등록하는 절차는 다음과 같다. ![](../../.gitbook/assets/install_bootstrap_flow.png)

#### 2.3.1. **스템셀 다운로드**

스템셀에 대한 원본 파일은 다음의 참조 사이트에서 다운로드가 가능하다. 로컬에서 다운로드 후 Inception에 업로드하여 Bosh Manifest에 포함시킬 수 있으며 Bosh Manifest에 URL 형태로도 지정 가능하다.

**1.    BOSH 스템셀**

1.1. BOSH 스템셀 참조 사이트

| 인프라 환경 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
|  Cloudit |  [https://s3.amazonaws.com/bosh-core-stemcells/315.22/bosh-stemcell-315.22-warden-boshlite-ubuntu-xenial-go\_agent.tgz](paas-ta_platform_install_automation_cloudit_v1.0.md) |
|  {PaaS-TA-Project}/kubernetes/cpi.yml에 스템셀이 url 형태로 지정되어 있으며 해당 Manifest는 프로젝트에 포함되어 있음 |  |

#### 2.3.2. **릴리즈 다운로드**

BOOTSTRAP을 설치하기 위해서는 BOSH 릴리즈와 BOSH CPI릴리즈 2개의 릴리즈가 필요하다. 릴리즈 다운로드 유형은 총 3가지이며 로컬에서 다운로드 후 Inception에 업로드하여 Bosh Manifest에 포함시킬 수 있으며 Bosh Manifest에 URL 형태로도 지정이 가능하다.

**1.    BOSH 릴리즈**

1.1. BOSH 릴리즈

| 릴리즈명 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
|  Bosh |  [https://s3.amazonaws.com/bosh-core-stemcells/315.22/bosh-stemcell-315.22-warden-boshlite-ubuntu-xenial-go\_agent.tgz](paas-ta_platform_install_automation_cloudit_v1.0.md) |
|  {PaaS-TA-Project}/kubernetes/cpi.yml에 스템셀이 url 형태로 지정되어 있으며 해당 Manifest는 프로젝트에 포함되어 있음 |  |

**2.    BOSH-Kubernetes CPI 릴리즈**

2.1. BOSH-Kubernetes CPI 릴리즈 참조 사이트

| 릴리즈명 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
|  Bosh-Kubernetes-CPI |  https://github.com/bosh-cpis/bosh-kubernetes-cpi-release |
|  {PaaS-TA\_Project}/Kubernetes/cpi.yml에 릴리즈 url이 file 형태로 지정되어 있으며 해당 Manifest는 프로젝트에 포함되어 있음 |  |

2.2. bosh-kubernetes-cpi 릴리즈 생성

```text
$ git clone https://github.com/bosh-cpis/bosh-kubernetes-cpi-release.git
  # commitID 963d393c7c065e9b9d7a01b123bc45a0e2cfbffd 기준
$ cp bosh-kubernetes-cpi-release
$ ./update-deps
$ bosh create-release --sha2 --force --tarball ./bosh-kubernetes-cpi.tgz --name bosh-kubernetes-cpi-release
$ cp bosh-kubernetes-cpi.tgz ~/workspace/{PaaS-TA_Project}/releases/
```

**3.    BPM 릴리즈**

3.1. BPM 릴리즈 참조 사이트

| 릴리즈명 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
|  BPM |  [https://s3.amazonaws.com/bosh-compiled-release-tarballs/bpm-1.0.4-ubuntu-xenial-315.22-20190514-221122-71304512-20190514221136.tgz](paas-ta_platform_install_automation_cloudit_v1.0.md) |
|  {PaaS-TA-Project}/bosh-deployment/bosh.yml 의 releases name : bpm 항목에 지정되어 있으며 해당 Manifest는 프로젝트에 포함되어 있음 |  |

#### 2.3.3. **BOOTSTRAP 설치 전 사전 작업**

BOOTSTRAP 및 CF 배포를 진행하기 위해서는 Inception 서버에 아래 항목에 대한 사전작업을 해야 한다. ※ Inception 서버에 사전 설치 필요 목록

|  - [kubectl](paas-ta_platform_install_automation_cloudit_v1.0.md)  - [DockerHub-Account](paas-ta_platform_install_automation_cloudit_v1.0.md)  - [Uaac-CLI](paas-ta_platform_install_automation_cloudit_v1.0.md)  - [Bosh-CLI](paas-ta_platform_install_automation_cloudit_v1.0.md)  - [CF-CLI](paas-ta_platform_install_automation_cloudit_v1.0.md)  |
| :--- |


#### 2.3.4. BOOTSTRAP 설치

BOOTSTRAP을 설치하기 위해 Inception 서버에서 아래의 작업 과정을 수행한다.

**1. Inception에 Kubernetes 환경 구성**

Inception 서버에서 Kubernetes를 제어하기 위해 기존 구성된 Kubernetes Master에 존재하는 admin.config를 Inception 서버에 복사한다.

1.1. Cloudit 포털의 컴퓨트 메뉴를 통해 생성된 Inception 서버에 접속한다.

```text
$ ssh root@Inception-server-ip
```

1.2. Inception 서버에서 kube 설정파일 관련한 디렉토리를 생성한다.

```text
$ mkdir ~/.kube
$ ls -al ~/
```

![](../../.gitbook/assets/kubernetes_make_kubeconfig.png)

1.3. Kubernetes의 Master Node에 SSH 접속을 한다. \# inception 서버에서 Kubernetes master node로 ssh 접속을 한다.

```text
$ ssh root@kubernetes-master-ip
```

1.4. Kubernetes의 admin.conf를 Inception 서버의 ~/.kube 디렉토리로 복사한다. \# Kubernetes Master Node에서 Inception 서버로 Kubernetes master 인증서를 복사한다.

```text
$ /etc/kubernetes/admin.conf root@Inception-server-ip:~/.kube/config
```

**2.    Inception에서 Cloudit의 Kubernetes Cluster 정보 확인**

2.1. Inception 서버에서 아래 명령으로 Kubernetes cluster의 정보가 출력되는지 확인한다.

```text
$ kubectl cluster-info
```

![](../../.gitbook/assets/kubernetes_verify_cluterinfo.png)

2.2. Kubernetes의 node정보도 확인한다.

```text
$ kubectl get nodes -o wide
```

![](../../.gitbook/assets/kubernetes_verify_node.png)

2.3. Kubernetes의 Storage-Class 정보도 확인한다.

※ Storage-class에 대한 구성 은 별도 첨부 문서 참조 : [Cloudit_DCE–PaaS-TA_연동가이드.docx](https://github.com/hoony-lab/Guide/tree/ac5ab50c799daee3eba5c440435e567d37d362ea/use-guide/platform/cloudit/CLOUDIT_DCE-PAAS-TA_INTEGRATION_GUIDE.docx)

```text
$ kubectl get storageclass
```

![](../../.gitbook/assets/kubernetes_verify_storageclass.png)

**3.    CLOUDit deployment files 다운로드**

3.1. 아래의 Git Repository 경로를 통해 CLOUDit deployment files 다운로드 받는다.

```text
$ mkdir workspace
$ cd workspace
$ git clone https://github.com/PaaS-TA/Guide-4.0-ROTELLE.git
$ cp -r ./Guide-4.0-ROTELLE/Use-Guide/platform/CLOUDit  {PaaS-TA-Project}
```

**4.    Micro-Bosh에 배포할 Kubernetes Assets 생성**

Assets 대상 : namespace, CPI-RBAC, bosh-external, Docker-registry-secret 4.1. Namespace를 생성한다.

```text
$ cd  ~/workspace/{PaaS-TA-Project}/kubernetes/assets/
$ vi namespace.yaml
```

![](../../.gitbook/assets/kubernetes_verify_assets.png)

\# 아래 명령어를 통해 Namespace를 생성한다.

```text
$ kubectl create -f namespace.yaml -n paasta
```

4.2. Namespace 가 생성된 것을 확인한다.

```text
$ kubectl get namespace
```

![](../../.gitbook/assets/kubernetes_verify_namespaces.png)

4.3. CPI-RBAC을 생성한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}/kubernetes/assets/
$ vi cpi-rbac.yaml
```

![](../../.gitbook/assets/kubernetes_verify_role.png)

\# Role 생성 및 Role Binding을 한다.

```text
$ kubectl create -f cpi-rbac.yml -n paasta
```

4.4. CPI-RBAC이 생성된 것을 확인한다. \# Role 확인

```text
$ kubectl get role -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_role_1.png)

\# Role 상세 확인

```text
$ kubectl get role bosh-cpi-manager -n paasta -o=yaml
```

![](../../.gitbook/assets/kubernetes_verify_role_2.png)

\# Role-Binding 확인

```text
$ kubectl get rolebindings -n paasta -o custom-columns='KIND:kind,NAMESPACE:metadata.namespace,NAME:metadata.name,SERVICE_ACCOUNTS:subjects[?(@.kind=="ServiceAccount")].name'
```

![](../../.gitbook/assets/kubernetes_verify_rolebinding.png)

\# Role-Binding 상세 확인

```text
$ kubectl describe rolebindings bosh-cpi-binding -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_rolebinding_1.png)

4.5. Routing Service인 Bosh-External을 생성한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}/kubernetes/assets/
$ vi bosh-external.yaml
```

![](../../.gitbook/assets/kubernetes_verify_boshexternal.png) ※ 각 항목들에 대한 nodePort 값을 확인한다.

```text
$ kubectl create -f bosh-external.yaml -n paasta
```

4.6. Routing Service인 Bosh-External이 적용되었는지 확인한다.

```text
$ kubectl get services bosh-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_boshexternal_1.png)

4.7 Docker Registry에 접근가능한 secret을 등록한다. \# Docker registry secret을 등록한다.

```text
$ kubectl create secret docker-registry {시크릿명. Ex). regsecret} --docker-server=https://registry.hub.docker.com --docker-username=docker-user-name --docker-password=docker-user-password --docker-email=docker-user-email@mail.com -n paasta
```

※ --docker option

docker-username : docker hub의 user명

docker-password : docker hub user의 password

docker-email : docker hub 계정의 email address

\# Docker registry secret이 등록되었는지 확인한다.

```text
$ kubectl get secret -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_secret.png)

**5. Bosh-Deployment 수정 및 배포**

5.1. bosh.yml 파일 수정을 한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}/bosh-deployment/
$ vi bosh.yml
```

**Note:** Comment or remove static\_ips under networks: ![](../../.gitbook/assets/kubernetes_verify_bosh.png)

5.2. Kubernetes Cluster에 Micro-Bosh를 배포하기 위한 create-env 스크립트 작성을 한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}/
$ vi bosh-deploy-kubernetes.sh
```

![](../../.gitbook/assets/kubernetes_create_env_bosh.png)

※ 스크립트의 옵션 정보는 다음과 같다.

```text
bosh create-env bosh-deployment/bosh.yml  # Bosh Environment 생성을 하며 bosh.yml 을 참조한다.
--state=kubernetes/state.json             # Bosh 설치 시 생성되는 state 파일
--vars-store=kubernetes/creds.yml         # Bosh 내부 인증서 파일
-o kubernetes/cpi.yml                     # Kubernetes CPI Release YAML
-o kubernetes/registry.yml                # Kubernetes CPI and Docker HUB YAML
-o bosh-deployment/jumpbox-user.yml       # jumpbox user
-o bosh-deployment/local-dns.yml          # local dns
-v director_name=paasta                   # Bosh director 명
-v internal_cidr="unused"                 # Internal IP Range
-v internal_gw="unused"                   # Internal Gateway IP
# internal_ip : 아래 내용 참조
# Micro-bosh 배포 전 해당 ip는 미리 Cloudit 포털의 네트워크 > 로드밸런싱 메뉴를 통해 미리 로드밸런서 IP의 가용여부를
 # 확보 해 놔야 한다.
# 현재 CLI 구성 Step에서는 Micro-bosh 배포 진행 도중, 어느 Worker Node에 Micro-bosh가 배포되고 있는지
# Pod를 실시간으로 조회하여 확인 후, 해당 Worker Node에 로드밸런서IP를 부여해야 한다.
# 이때 internal_ip 항목에 있는 IP와 Cloudit 포털의 로드밸런서 IP가 같아야 한다.
-v internal_ip="192.168.x.x"
-v local_host="127.0.0.1"

# kube_config : Inception 서버 해당 경로에 kubernetes cluster configuration 위치를 지정한다.
--var-file kube_config=<(cat ~/.kube/config)

# storage_class : Kubernetes Cluster의 Storage-Class 명을 지정한다.
# Storage-Class명은 "kubectl get storageclass" 명령으로 확인 가능 하다.
-v storage_class=paasta-rbd                 
# {Nortport}_nodeport : "bosh-ingress” 라는 서비스 내용 중 Nodeport 항목과 매핑 되는 정보다.
-v reg_backend=docker
-v reg_host="registry.hub.docker.com"          # Docker Hub Registry Domain  
-v reg_url="https://registry.hub.docker.com"   # Docker Hub Registry URL
-v reg_user=dockerhubusername                  # Docker Hub Registry User 명
-v reg_password=dockerhubuserpass              # Docker Hub Registry User의 Password
-v paasta_namespace=paasta                     # Namespace 명 지정
-v reg_pull_secret_name=regsecret              # Docker Hub Registry의 Secret 명 지정

# Kubernetes Cluster API의 Address와 Port 주소 지정
# 해당 정보는 아래 명령어로 확인 가능하다.
# kubectl cluster-info
-v kube_api=10.0.0.21                          # Kubernetes Master의 API 주소
-v kube_api_port=6443                          # Kubernetes Master의 API 포트
```

5.3. Kubernetes Cluster에 Micro-Bosh 배포하기 위해 create-env 스크립트 수행을 한다.

```text
$ . bosh-deploy-kubernetes.sh
```

![](../../.gitbook/assets/kubernetes_create_env_bosh_1.png)

**\# Bosh 배포 시 create VM for instance step 이후 agent 단계에서 Waiting 하게 된다. pod를 조회하여 어느 Worker Node에 Bosh가 배포되는지 확인 후 해당 Node를 로드밸런서의 멤버로 등록해야 한다.**

\# 다음은 nodeport의 확인 절차이다.

```text
$ kubectl describe services bosh-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_nodeport.png)

\# 아래 예제에선 paasta-5 node에 Bosh가 배포된다.

```text
$ kubectl get pods -n paasta -o wide
```

![](../../.gitbook/assets/kubernetes_verify_bosh_vm.png) \# 위의 정보를 기반으로 Cloudit 포털에서 로드밸런싱을 구성한다. \([4.Cloudit 로드밸런서생성 – Micro-Bosh 연결](paas-ta_platform_install_automation_cloudit_v1.0.md#22) 참조\).

5.4 Micro-Bosh에 접근하기 위한 접속 정보\(Credentials\)와 Alias를 지정한다. \# Alias 지정을 하며 이후 해당 env명으로 접근가능 하다.

```text
$ bosh alias-env paasta -e {Micro-Bosh-Loadbalancing-IP} --ca-cert <(bosh int ~/workspace/{PaaS-TA-Project}/kubernetes/creds.yml --path /director_ssl/ca)
```

\# 아래 입력한 값으로 인증하도록 환경변수를 지정한다.

```text
$ export BOSH_CLIENT=admin


$ export BOSH_CLIENT_SECRET=\`bosh int ~/workspace/{PaaS-TA-Project}/kubernetes/creds.yml --path /admin_password\`
```

\# Director 명을 지정하여 Env가 출력되는지 확인한다.

```text
$ bosh -e paasta env
```

![](../../.gitbook/assets/kubernetes_verify_bosh_env.png)

5.5 Micro-Bosh VM으로 Bash Shell이 접속되는지 확인한다. \# Micro-Bosh가 Deploy 되어 있는 pod를 확인한다.

```text
$ kubectl get pods -n paasta -o wide
```

\# 확인된 pod명으로 접속을 시도한다.

```text
$ kubectl exec -it --namespace=paasta {POD명} -- /bin/bash
```

\# bosh/0:/\# 와 같이 bosh의 bash shell 이 출력되면 접속 성공이다.

```text
bosh/0:/#
```

### 2.4.  **CF-Deployment 배포하기**

BOSH를 배포하고 BOSH에 대한 인증정보까지 설정이 완료되면 CF-Deployment를 배포할 준비가 된 상태이며 CF-Deployment를 배포하는 절차는 다음과 같다. ![](../../.gitbook/assets/install_cf_flow.png)

#### 2.4.1. **스템셀 업로드**

PaaS-TA-4.0 Offline Package에서 bosh-stemcell-97.28-warden-boshlite-ubuntu-xenial-go\_agent.tgz을 추출후에 스템셀을 업로드한다.

**1. 스템셀 업로드**

1.1 추출된 스템셀을 업로드한다.

```text
$ cd ~/workspace/releases/
```

\# 아래 명령어를 통해 스템셀을 업로드 한다.

```text
$ bosh -e paasta upload-stemcell bosh-stemcell-97.28-warden-boshlite-ubuntu-xenial-go_agent.tgz
```

1.2 정상적으로 스템셀이 업로드 되었는지 확인한다. \# 업로드 된 스템셀을 확인한다.

```text
$ bosh stemcells -e paasta
```

#### 2.4.2. **Cloud-Config와 Runtime-Config 설정**

IaaS 관련 Network/Storage/VM 관련 설정들을 정의하는 Cloud-Config와 PaaS-TA Component간의 통신을 위한 Bosh DNS등등의 Runtime Config를 설정한다.

**1. Cloud-Config 업데이트**

1.1 Cloud-config 내용을 업데이트 한다. \# 아래 명령어를 통해 cloud-config 내용을 업데이트 한다.

```text
$ bosh -e paasta update-cloud-config ~/workspace/{PaaS-TA-Project}/kubernetes/cloud-config.yaml
```

**2. Run-Time-Config 업데이트**

2.1 Run-Time-Config 내용을 업데이트 한다. \# 아래 명령어를 통해 run-time-config 내용을 업데이트 한다.

```text
$ bosh -e paasta update-runtime-config ~/workspace/{PaaS-TA-Project}/bosh-deployment/runtime-configs/dns.yml --vars-store ~/workspace/{PaaS-TA-Project}/cf-kubernetes/runtime-config-creds.yml
```

2.2 정상적으로 Run-Time-Config가 업데이트 되었는지 확인한다. \# 적용된 run-time-config 내용을 확인을 한다.

```text
$ bosh runtime-config -e paasta
```

#### 2.4.3. **CF-Deployment 배포**

CF Deployment 설치하기 전 Routing Service 및 CF에 대한 각종 환경들을 설정 후 CF Deployment를 설치한다.

**1.    Routing Service인 CF-External 생성**

1.1. CF-External Service를 생성한다. \# CF API에 login하기 위한 Routing Service에 대한 cf-external.yaml 파일 확인

```text
$ cd ~/workspace/{PaaS-TA-Project}/kubernetes/assets/
$ vi cf-external.yaml
```

![](../../.gitbook/assets/kubernetes_verify_cfexternal.png)

※ http와 https의 NodePort 정보 확인  
\# HTTP 및 HTTPS\(SSL\)Protocol에 대한 NodePort를 확인하여 CF 배포 이후 로드밸런서와 연결한다.  
\# 관련 내용은 2.5.3.4 Cloudit 로드밸런싱 설정을 참조한다.  
\# Routing Service를 생성한다.

```text
$ kubectl create -f cf-external.yaml -n paasta
```

1.2. CF-External Service가 생성되었는지 확인한다.

```text
$ kubectl get service cf-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_cfexternal_1.png)

$ kubectl describe service cf-ingress -n paasta ![](../../.gitbook/assets/kubernetes_verify_cfexternal_2.png)

**2.    CF-Deployment 배포 정보 설정**

2.1. CF-Deployment 배포 정보를 설정한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}/cf-deployment/
```

\# cf-deployment에 대한 설정 수정 \(변경할 사항 없음\)

```text
$ vi cf-deployment.yml
```

**3.    Kubernetes Cluster에 CF-Deployment\(PaaS-TA Core\) 배포**

3.1. CF를 Deploy하기 위한 manifest 파일과 옵션에 대해 수행 스크립트를 작성한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}
```

\# HAProxy를 사용하지 않을 경우의 스크립트는 다음과 같다.

```text
$ vi cf-deploy-kubernetes.sh
```

![](../../.gitbook/assets/kubernetes_create_deploy_cf.png)

※ 스크립트의 옵션 정보는 다음과 같다.  
\# cf-deployment에 대한 정의이며 -d 옵션은 deployment 명을 지정한다.

```text
bosh -e paasta -d cf deploy -n cf-deployment/cf-deployment.yml  
--vars-store cf-kubernetes/creds.yml                          # Credentials를 저장한다.
-o cf-deployment/operations/use-compiled-releases.yml         # PaaS-TA Release에 대한 정의이다.
-o cf-deployment/operations/scale-to-one-az.yml     # Cloud-config 상에 정의되는 available zone에 대한 정의이다.
# system_domain : 표기되는 IP주소는 CF login의 API 주소이며 로드밸런서의 IP 주소이다.
# CF Deploy 이전에 Cloudit 포탈의 가용한 로드밸런서의 IP를 미리 확보 후, 해당 IP(사용할)를 입력한다.
# CF Deploy 이후 입력된 로드밸런서의 IP를 2.1.5 인프라 설정의 Cloudit 로드밸런서 생성 항목을 참고하여
# 로드밸런싱 구성을 한다.
-v system_domain="192.168.x.x.xip.io"
```

\# HAProxy를 사용할 경우의 스크립트는 다음과 같다.

```text
$ vi cf-deploy-kubernetes.sh
```

![](../../.gitbook/assets/kubernetes_create_deploy_cf_with_haproxy.png)

※ 스크립트의 옵션 정보는 다음과 같다.  
\# cf-deployment에 대한 정의이며 -d 옵션은 deployment 명을 지정한다.

```text
bosh -e paasta -d cf deploy -n cf-deployment/cf-deployment.yml  
--vars-store cf-kubernetes/creds.yml                          # Credentials를 저장한다.
-o cf-deployment/operations/use-compiled-releases.yml         # PaaS-TA Release에 대한 정의이다.
-o cf-deployment/operations/scale-to-one-az.yml               # Cloud-config 상에 정의되는 available zone에 대한 정의이다.
-o cf-deployment/operations/use-haproxy.yml                   # HAProxy Release에 대한 정의이다.
-o cf-deployment/operations/use-haproxy-public-network.yml    # HAProxy 사용시 public network에 대한 정의이다.
# system_domain : 표기되는 IP주소는 CF login의 API 주소이며 로드밸런서의 IP 주소이다.
# CF Deploy 이전에 Cloudit 포탈의 가용한 로드밸런서의 IP를 미리 확보 후, 해당 IP를 입력한다.
# CF Deploy 이후 입력된 로드밸런서의 IP를 2.1.5 인프라 설정의 Cloudit 로드밸런서 생성 항목을 참고하여
# 로드밸런싱 구성을 한다.
-v system_domain="192.168.x.x.xip.io"
```

\# 스크립트에 실행 권한을 준다.

```text
$ chmod 755 cf-deploy-kubernetes.sh
```

3.2. CF Deployment를 배포한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}
```

\# CF 배포 스크립트를 수행한다.  
\# PaaS-TA\(CF-Deployment\) 배포를 실행하고 배포 진행 과정에 대해 정상적으로 배포가 수행되는지 로그를 필히 확인한다.

```text
$ ./cf-deploy-kubernetes.sh
```

**4.    Cloudit 로드밸런싱 설정**

4.1. CF 배포 이후 CF Login을 하기 위해 Router\(또는 HAProxy\)가 배포된 VM을 확인한다.  
\# bosh를 통해 배포되어 사용중인 VM을 확인한다.

```text
$ bosh -e paasta -d cf vms
```

\# 아래 예제의 경우는 HAProxy를 사용하지 않았을 경우이며, 해당 케이스의 경우는 router instance에 대한 VM CID를 참고한다.  
\# 만약 HAProxy를 사용하는 경우라면, router보다 haproxy가 아키텍쳐상 상단에 위치하기 때문에 router instance 대신 haproxy instance에 대한 VM CID를 참고해야 한다. ![](../../.gitbook/assets/kubernetes_verify_router_vm.png)

\# 위 출력 결과 중 router\(또는 haproxy\) instance의 VM CID를 확인한다.  
\# 아래 명령어를 통해 위에서 얻은 VM CID 값으로 router\(또는 haproxy\)가 어느 Worker node에 배포되었는지 확인한다.  
\# 아래 예제의 경우는 NODE 컬럼을 통해 paasta-3 node에 router\(또는 haproxy\)가 배포되어 있는 것을 확인할 수 있다.

```text
$ kubectl get pod -o wide -n paasta | grep vm-117978d5-be6e-4566-7ec3-03aa7a9ee7d7
```

![](../../.gitbook/assets/kubernetes_verify_router_vm_1.png)

4.2. Cloudit 로드밸런서 생성에 따라 로드밸런싱 설정을 한다. ※ [2.1.5 Cloudit 로드밸런서 생성 – Router/HAProxy 배포 VM](paas-ta_platform_install_automation_cloudit_v1.0.md#23)

※ 로드밸런싱 설정 시 다음의 정보를 따른다.  
1. 로드밸런서의 IP는 CF-Deploy시 사용했던 system\_domain의 IP주소를 입력한다.  
2. 로드밸런서 구성 요소 중 서버IP 항목의 물리 VM은 router가 배포된 VM을 지정한다. \(또는 haproxy\)  
3. 로드밸런서 구성 요소 중 멤버 항목의 서버포트에 대한 정보는 cf-ingress service 항목을 참고한다. 80과 443에 대한 nodePort를 지정한다.  
\# ingress 정보 확인  
\# 80과 443 포트와 매핑 된 NodePort에 대해 로드밸런싱 포트를 추가한다.  
\# 아래 예제에선 80:30274/TCP의 내용과 443:30732/TCP의 내용이 여기에 해당된다.

```text
$ kubectl get svc cf-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_nodeport%20%281%29.png)

**5.    CF Login**

5.1. CF에 로그인한다. \# 아래 명령어를 통해 배포된 CF에 로그인을 한다.  
\# cf login시 -p 옵션에 해당하는 cf\_admin\_password는 아래의 경로에서 확인 가능하다.

```text
$ ~/workspace/{PaaS-TA-Project}/cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_cf_admin_password.png)

\# CF Login의 Target 주소는 CF Deployment정의서의 정의된 system\_domain 항목이며 해당 옵션에 표기된 IP는 로드밸런서의 IP이다.

```text
$ cf login -a https://api.192.168.x.x.xip.io -u admin -p cf_admin_passsword --skip-ssl-validation
```

![](../../.gitbook/assets/kubernetes_login_cf_url.png)

**6.    CF Space 생성**

6.1. CF의 Org를 확인한다. \# 아래 명령어를 통해 배포된 CF에 org를 확인한다.

```text
$ cf orgs
```

![](../../.gitbook/assets/kubernetes_verify_cf_orgs.png)

6.2. CF의 Space를 생성한다. \# 아래 명령어를 통해 배포된 CF의 org에 space를 생성한다.

```text
$ cf create-space cf-test-01 -o system
```

![](../../.gitbook/assets/kubernetes_create_cf_space.png)

6.3. CF의 Space Target 지정 \# 아래 명령어를 통해 배포된 CF org의 space에 Target 지정을 한다.

```text
$ cf target -s cf-test-01
```

![](../../.gitbook/assets/kubernetes_point_cf_target.png)

#### 2.4.4. **CF App Push 테스트**

CF Deployment 배포 이후 App Push 테스트를 진행한다.

**1.    CF Push app**

1.1. 샘플 App을 다운로드 한다.

```text
$ git clone https://github.com/cloudfoundry-samples/cf-sample-app-nodejs.git
$ cd cf-sample-app-nodejs
```

1.2. CF Push를 한다.  
\# 다음의 명령어로 cf push를 하며 아래의 routes 항목을 통해 URL을 얻어온다.

```text
$ cf push
```

![](../../.gitbook/assets/kubernetes_push_cf_app.png)

1.3. CF Apps를 조회한다.  
\# cf apps 명령어를 통해 app 상태 조회를 한다.

```text
$ cf apps
```

![](../../.gitbook/assets/kubernetes_show_cf_apps.png)

1.4. App 접속 테스트 \# 다음의 명령어와 위 Step에서 조회된 URL을 통해 해당 URL에 접속을 한다.

```text
$ curl cf-nodejs-fantastic-emu.192.168.x.x.xip.io
```

![](../../.gitbook/assets/kubernetes_verify_cf_app.png)

#### ※ **별첨 : DCE 클러스터 연동**

첨부 문서 참조 : [CLOUDIT\_DCE–PAAS-TA\_INTEGRATION\_GUIDE.docx](https://github.com/hoony-lab/Guide/tree/ac5ab50c799daee3eba5c440435e567d37d362ea/use-guide/platform/cloudit/CLOUDIT_DCE-PAAS-TA_INTEGRATION_GUIDE.docx)

