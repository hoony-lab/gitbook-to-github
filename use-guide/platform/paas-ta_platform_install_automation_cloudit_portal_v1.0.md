# PAAS-TA\_PLATFORM\_INSTALL\_AUTOMATION\_CLOUDIT\_PORTAL\_v1.0

## Table of Contents

1. [문서 개요](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#1)
   * [목적](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#2)
   * [범위](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#3)
2. [플랫폼 설치 가이드](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#4)
   * [인프라 설정](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#5)
   * [스템셀과 릴리즈](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#6)
     * [PaaS-TA 사용자 포털 스템셀](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#8)
   * [BOOTSTRAP 및 PaaS-TA 설치하기](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#9)
   * [PaaS-TA 사용자 포털 배포하기](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#14)
     * [PaaS-TA 사용자 포털 스템셀 업로드](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#15)
     * [PaaS-TA 사용자 포털 릴리즈 업로드](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#16)
     * [Routing Service 설정](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#17)
     * [PaaS-TA 사용자 포털 배포 전 사전 점검](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#18)
     * [PaaS-TA 사용자 포털 배포](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#19)

## Deprecated

**※ 본 문서는 PaaS-TA 5.0.1 이하의 버전까지 지원한다.**

## 1.  문서 개요

### 1.1.  목적

본 문서는 CLI 기반으로 PaaS-TA 사용자 포털을 구축하는 절차에 대해 기술하였다.

### 1.2.  범위

본 문서에서는 Cloudit 인프라 환경에서 Kubernetes 기반의 PaaS-TA 사용자 포털을 설치하는 방법에 대해 작성되었다.

## 2.  플랫폼 설치 가이드

BOSH는 클라우드 환경에 서비스를 배포하고 소프트웨어 릴리즈를 관리해주는 오픈 소스로 Bootstrap은 하나의 VM에 설치 관리자의 모든 컴포넌트를 설치한 것으로 PaaS-TA 사용자 포털 설치를 위한 관리자 기능을 담당한다. Cloudit 환경의 Bootstrap은 물리적인 하나의 VM이 아닌 컨테이너 기반으로 동작한다.

Cloudit 클라우드 환경에 PaaS-TA포털을 설치하기 위해서는 인프라 설정, 스템셀 소프트웨어 릴리즈, Manifest 파일, 인증서 파일 5가지 요소가 필요하다. 스템셀은 클라우드 환경에 VM을 생성하기 위해 사용할 기본 이미지이고, 소프트웨어 릴리즈는 VM에 설치할 소프트웨어 패키지들을 묶어 놓은 파일이고, Manifest파일은 스템셀과 소프트웨어 릴리즈를 이용해서 서비스를 어떤 식으로 구성할지를 정의해 놓은 명세서이다. 다음 그림은 BOOTSTRAP을 이용하여 PaaS-TA 사용자 포털을 설치하는 절차이다.

![](../../.gitbook/assets/install_portal_flow%20%281%29%20%281%29%20%281%29.png)

### 2.1  인프라 설정

※ BOSH 및 PaaS-TA 설치\(CLOUDit\) [2.1 인프라 설정](paas-ta_platform_install_automation_cloudit_v1.0.md#5) 참조

**1. Cloudit 로드밸런서 생성 – HAProxy배포 VM \(2.4.5. PaaS-TA 사용자 포털 배포 이후 진행\) – 네트워크**

1.1. 로드밸런서 생성 중 Router 또는 HAProxy가 배포된 VM을 멤버로 등록시엔 다음과 같은 포트로 구성한다

| 대상 | 용도 | LB 포트 | 서버 포트 | 비고 |
| :--- | :--- | :--- | :--- | :--- |
|  HAProxy가 배포된 VM | 포털 | 80 | 30602 |  |
| 유레카 | 2221 | 30702 |  |  |

1.2. Cloudit 포탈에 접속한다. 접속 주소: [https://www.cloudit.co.kr](paas-ta_platform_install_automation_cloudit_portal_v1.0.md)  
1.3. 네트워크 메뉴를 클릭한다.  
1.4. 로드밸런싱 메뉴를 클릭한다.  
1.5. 로드밸런싱 화면에서 “Create” 버튼을 클릭한다.

![](../../.gitbook/assets/lb_bosh_list_lb%20%282%29%20%282%29.png)

1.5.1. 로드밸런싱 생성 팝업에서 기본 정책을 입력 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_portal_choice_basicpolicy.png)

※ 이름 : 로드밸런서 이름 입력  
포트 : 로드밸런서 Port 입력 \(상단 1.1 포트 구성표의 [LB 포트 항목](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#23) 참조\)  
유형 : 로드밸런서 IP 종류 선택  
\(External : 로드밸런싱에서 이용할 Public VIP로 지정하여 사용  
Internal : 로드밸런싱 서브넷에서 사용\)  
IP : 로드밸런서의 IP 이며 동일 IP로 여러 개의 Port 설정 가능하다. 해당 샘플에서는 Router 또는 HAProxy가 배포된 VM에 부여할 로드밸런싱 IP를 선택한다.  
프로토콜 : TCP 또는 HTTP 프로토콜  
정책 : 4가지 Load Balancing 정책 중 선택

|  **Round Robin** : 순차적으로 세션을 연결하는 정책, 거의 균등한 부하 분산이 가능하나 세션 유지 불가능  **Least Connection** : 세션 요구량이 적은 쪽으로 신규 세션을 연결해주는 정책  **Source Hash** : 출발지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 목적지로 세션을 연결해주는 정책  **Destination Hash** : 목적지의 IP 주소를 기반으로 Hash를 계산하여 항상 같은 출발지와 세션을 연결해주는 정책 |
| :--- |


1.5.2. 구성할 로드밸런서의 상태 체크 설정 및 멤버 등록 후 다음 버튼을 클릭한다. ![](../../.gitbook/assets/lb_portal_add_member.png)

※ 모니터 타입 : 상태 체크 프로토콜 선택  
http 경로 : 모니터 타입이 HTTP인 경우 상태 체크 할 URL 입력  
응답대기 시간 : 로드밸런싱 응답 대기 시간 입력  
정상 판단 횟수 : 응답 대기 시간 동안 응답하지 않을 때 정상 판단 횟수  
상태 확인 주기\(초\) : 로드밸런싱 상태 확인 주기  
비정상 판단 횟수 : 상태 확인 주기동안 응답하지 않은 때 비정상 판단 횟수  
서버 IP : 로드밸런싱 정책에 의해 작동 될 물리 VM의 IP선택  
서버 포트 : 로드밸런싱 정책에 의해 작동 될 물리 VM의 Port 선택  
\(상단 [1.1 포트 구성표](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#23)의 서버 포트 항목 참조\)

1.5.3 구성할 로드밸런싱의 최종 정보를 확인 후 확인 버튼을 클릭한다. ![](../../.gitbook/assets/lb_portal_verify_finalinfo.png)

### 2.2.  스템셀과 릴리즈

#### 2.2.1. **PaaS-TA 사용자 포털 스템셀**

Cloudit의 Kubernetes 환경에 배포 가능한 PaaS-TA 버전은 아래와 같다. 아래의 릴리즈 버전으로 다운로드&업로드 및 설치한다.

| 릴리즈 | 스템셀 |
| :--- | :--- |
| paasta/4.0 |  https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.51-warden-boshlite-ubuntu-trusty-go\_agent.tgz |
|  https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.21-warden-boshlite-ubuntu-trusty-go\_agent.tgz |  |

### 2.3.  **BOOTSTRAP 및 PaaS-TA 설치하기**

BOSH 및 PaaS-TA 설치\(CLOUDit\) [2.3 BOOTSTRAP 설치하기](paas-ta_platform_install_automation_cloudit_v1.0.md#9) 참조  
BOSH 및 PaaS-TA 설치\(CLOUDit\) [2.4. CF-Deployment 배포하기](paas-ta_platform_install_automation_cloudit_v1.0.md#14) 참조

### 2.4.  **PaaS-TA 사용자 포털 배포하기**

BOSH 및 PaaS-TA 배포가 완료되면 PaaS-TA 사용자 포털을 배포할 준비가 된 상태이며 PaaS-TA 사용자 포털을 배포하는 절차는 다음과 같다. ![](../../.gitbook/assets/install_portal_flow%20%281%29%20%281%29.png)

#### 2.4.1. **PaaS-TA 사용자 포털 스템셀 업로드**

URL을 통해 bosh-stemcell-3468.51-warden-boshlite-ubuntu-trusty-go\_agent.tgz, bosh-stemcell-3468.21-warden-boshlite-ubuntu-trusty-go\_agent.tgz 스템셀을 다운로드 후에 해당 스템셀을 Bosh에 업로드한다.

**1. PaaS-TA 사용자 포털 스템셀**

1.1 PaaS-TA 사용자 포털 스템셀 참조 사이트

| 인프라 환경 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
| paasta/4.0 |  [https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.21-warden-boshlite-ubuntu-trusty-go\_agent.tgz](paas-ta_platform_install_automation_cloudit_portal_v1.0.md) |
|  [https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.51-warden-boshlite-ubuntu-trusty-go\_agent.tgz](paas-ta_platform_install_automation_cloudit_portal_v1.0.md) |  |

1.2 스템셀을 다운로드 후 Bosh에 업로드 한다.

```text
$ cd ~/workspace/releases/
```

\# 아래 명령어를 통해 스템셀을 다운로드 한다.

```text
$ wget https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.51-warden-boshlite-ubuntu-trusty-go_agent.tgz
$ wget https://s3.amazonaws.com/bosh-core-stemcells/warden/bosh-stemcell-3468.21-warden-boshlite-ubuntu-trusty-go_agent.tgz
```

\# 아래 명령어를 통해 스템셀을 업로드 한다.

```text
$ bosh -e paasta upload-stemcell bosh-stemcell-3468.21-warden-boshlite-ubuntu-trusty-go_agent.tgz
$ bosh -e paasta upload-stemcell bosh-stemcell-3468.51-warden-boshlite-ubuntu-trusty-go_agent.tgz
```

1.3 정상적으로 스템셀이 업로드 되었는지 확인한다.  
\# 업로드 된 스템셀을 확인한다.

```text
$ bosh -e paasta stemcells
```

#### 2.4.2. **PaaS-TA 사용자 포털 릴리즈 업로드**

PaaS-TA 사용자 포털 릴리즈를 다운로드 후 bosh에 릴리즈 생성 후 릴리즈를 업로드 한다.

**1. PaaS-TA 사용자 포털 릴리즈**

1.1 PaaS-TA 사용자 포털 릴리즈 참조 사이트

| 릴리즈명 | 참조 사이트 및 참조 Manifest |
| :--- | :--- |
| paasta/4.0 |  https://github.com/PaaS-TA/PAAS-TA-PORTAL-RELEASE의 v4.0-container Branch |
|  소스를 통해 릴리즈를 생성한다. |  |

1.2. 아래의 Git Repository 경로를 통해 PaaS-TA 사용자 포털 Release for container를 생성하기 위한 소스를 다운로드 받는다.

```text
$ cd workspace
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-RELEASE -b v4.0-container && cd PAAS-TA-PORTAL-RELEASE
$ cd ./PAAS-TA-PORTAL-RELEASE
$ wget -O src.zip http://45.248.73.44/index.php/s/JAQRFctz7Tn26qK/download
$ unzip src.zip
$ rm -rf src.zip
```

**2.    PaaS-TA 사용자 포털 Release for container 생성**

2.1. 릴리즈를 생성한다.  
\# PaaS-TA 사용자 포털 for Container 릴리즈 생성

```text
$ bosh create-release --sha2 --force --tarball ./paasta-portal-release-4.0-container.tgz --name paasta-portal-release --version 4.0-container
```

**3.    PaaS-TA 사용자 포털 릴리즈 Bosh에 업로드**

3.1. 준비된 릴리즈를 Bosh에 업로드 한다.

```text
$ cd ~/workspace/PAAS-TA-PORTAL-RELEASE
$ bosh -e paasta upload-release ./paasta-portal-release-4.0-container.tgz
```

1.2. 릴리즈가 정상적으로 업로드 되었는지 확인한다.

```text
$ bosh -e paasta releases
```

#### 2.4.3. **Routing Service 설정**

PaaS-TA 사용자 포털을 배포하기 전 Routing Service를 설정한다.

**1. Routing Service인 Portal-Proxy-External 생성**

1.1. Portal-Proxy-External Service를 생성한다.  
\# 포털에 접근하기 위한 Routing Service인 portal-proxy-external.yaml 파일 확인

```text
$ cd {PaaS-TA-Project}/kubernetes/assets
$ vi portal-proxy-external.yaml
```

![](../../.gitbook/assets/kubernetes_verify_portalexternal.png)

※ http-proxy, eureka의 NodePort 정보 확인  
http-proxy, eureka에 대한 NodePort를 확인하여 PaaS-TA 사용자 포털 배포 이후 로드밸런서와 연결한다.

\# 관련 내용은 2.1.1 인프라 설정의 [Cloudit 로드밸런서 생성](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#23) 설정을 참조한다.  
\# Routing Service를 생성한다.

```text
$ kubectl create -f portal-proxy-external.yaml -n paasta
```

1.2. Portal-External Service가 생성되었는지 확인한다.  
\# portal-proxy와 eureka에 대한 NodePort를 확인한다.

```text
$ kubectl get service portal-user-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_portalexternal_1.png)

\# portal-proxy와 eureka에 대한 NodePort를 상세 확인한다.

```text
$ kubectl describe service portal-proxy-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_portalexternal_2.png)

#### 2.4.4. **PaaS-TA 사용자 포털 배포 전 사전 점검**

PaaS-TA 사용자 포털 배포 전 배포되는 릴리즈, IaaS 관련 Network/Storage/VM 관련 설정들을 정의하는 Cloud-Config 등의 Manifest 사전 점검을 한다.

**1.    PaaS-TA 사용자 포털 릴리즈 버전 점검**

1.1 PaaS-TA 사용자 포털 Manifest를 확인한다.  
\# 아래 명령어를 통해 Manifest 내용을 점검한다.

```text
$ vi ~/workspace/{PaaS-TA-Project}/paasta-portal-4.0-container.yml
```

![](../../.gitbook/assets/kubernetes_verify_container_yaml.png)

**2.    Cloud-config의 vm\_extensions 점검**

2.1 Cloud-config 내용을 확인한다.  
\# 아래 명령어를 통해 cloud-config의 vm\_extensions 항목의 내용을 확인한다.

```text
$ bosh -e paasta cloud-config
```

![](../../.gitbook/assets/kubernetes_verify_cloudconfig.png)

**3. UAA portalclient 생성**

3.1 portalclient를 생성한다.

```text
$ uaac target https://uaa.192.168.x.x.xip.io --skip-ssl-validation
```

![](../../.gitbook/assets/kubernetes_create_uaa_portalclient.png)

\# admin\_client\_secet은 {PaaS-TA-Project}/cf-kubernetes/creds.yml 에서 확인 가능하다.

```text
$ vi ~/workspace/{PaaS-TA-Project}/cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_uaa_adminclient.png)

```text
$ uaac token client get admin -s admin_client_secret
```

![](../../.gitbook/assets/kubernetes_verify_uaa_admin.token.png)

```text
# client를 추가한다.
$ uaac client add \
  --name portalclient \
  --scope cloud_controller_service_permissions.read,openid,cloud_controller.read,cloud_controller.write,cloud_controller.admin \
  --authorities uaa.resource \
  --redirect_uri http://portal-web-user.192.168.x.x.xip.io,http://portal-web-user.192.168.x.x.xip.io/callback \
  --authorized_grant_types authorization_code,client_credentials,refresh_token  \
  --secret portalclient \
--autoapprove openid,cloud_controller_service_permissions.read \
```

※ 옵션에 대한 정보는 다음과 같다 --name 은 client의 이름이다. --redirect\_uri의 ip주소는 user-portal이 사용할 로드밸런서의 IP가 된다. --secret은 client가 사용할 secret 명이다.

\# portalclient를 확인한다.

```text
$ uaac token client get portalclient -s portalclient
```

![](../../.gitbook/assets/kubernetes_verify_uaa_portalclient_token.png)

#### 2.4.5. **PaaS-TA 사용자 포털 배포**

PaaS-TA 포털을 설치하기 전 배포 관련된 각종 환경들을 스크립트에 설정 후 PaaS-TA 사용자 포털을 설치한다.

**1.    Kubernetes Cluster에 PaaS-TA 사용자 포털 배포**

1.1. PaaS-TA 사용자 포털을 배포하기 위한 정의파일과 옵션에 대해 수행 스크립트를 작성한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}
```

\# PaaS-TA 사용자 포털 배포를 위한 스크립트를 다음과 같이 정의한다.

```text
$ vi paasta-portal-deploy-kubernetes.sh
```

![](../../.gitbook/assets/kubernetes_create_portal_deployscript.png)

※ 스크립트의 옵션 정보는 다음과 같다.

```text
# PaaS-TA 사용자 포털의 deployment에 대한 정의이며 -d 옵션은 PaaS-TA 사용자 포털 deployment 명을 지정한다.
bosh -e paasta -d portal deploy -n paasta-portal-deployment/paastaportal-4.0-container.yml
--vars-store paasta-portal-deployment/vars.yml
-v releases_name="paasta-portal-release"           # 배포에 사용될 릴리즈 명을 명시한다.
-v stemcell_os="ubuntu-trusty"                     # 배포에 사용될 스템셀 명을 명시한다.
-v stemcell_version="3468.51"                      # 배포에 사용될 스템셀의 버전을 명시한다.
-v stemcell_alias="default"                        # 스템셀 alias 지정
-v vm_type_tiny="minimal"                          # vm의 tiny type 지정
-v vm_type_small="small"                           # vm의 small type 지정
-v vm_type_medium="small-highmem"                  # vm의 medium type 지정
-v internal_networks_name="default"                # internal 네트워크명 지정
-v external_networks_name="default"                # external 네트워크명 지정
-v mariadb_disk_type="10GB"                        # mariadb의 disk type 지정
-v mariadb_port="3306"                             # mariadb에서 사용할 port
-v mariadb_user_password="password12"              # mariadb에서 사용할 user password 지정
-v binary_storage_disk_type="10GB"                 # binary storage의 disk size 지정
-v binary_storage_username="paasta-portal"         # binary storage의 user명 지정
-v binary_storage_password="paasta"                # binary storage의 password 지정
-v binary_storage_tenantname="paasta-portal"       # binary storage의 tenant명 지정
-v binary_storage_email="paasta@paasta.com"        # binary storage의 email 지정

# haproxy_public_ip : 표기되는 IP주소는 PaaS-TA 사용자 포털 deployment의 haproxy instance랑 연결되어질 로드밸런서의 IP 주소이다.
# PaaS-TA 사용자 포털 배포 이전에 Cloudit 포탈의 가용한 로드밸런서의 IP를 미리 확보 후, 해당 IP(사용할)를 입력한다.
# PaaS-TA 사용자 포털 배포 이후 아래 옵션에 입력된 haproxy_public_ip의 IP를 2.1.1 인프라 설정의 [Cloudit 로드밸런서 생성](#23) 항목을 참고하여 로드밸런싱 구성을 한다.
-v haproxy_public_ip="192.168.x.x"

# cf_db_ips : PaaS-TA Core(CF Deployment)의 배포된 instance 중 database에 해당하는 IP를 지정한다.
# database instance의 IP 확인은 다음과 같다.
# $ bosh -e paasta -d cf(CF 배포명) vms
```

![](../../.gitbook/assets/kubernetes_verify_cf_db_instance.png)

```text
-v cf_db_ips="10.244.5.117"
-v cf_db_port="5524"                              # cf database의 port 지정
-v cc_db_id="cloud_controller"                    # cf database의 id 지정

# cc_db_password : cloud controller의 db password 이며 PaaS-TA Core(CF)의 cloud controller가 배포가
# 되어 있어야 한다. cloud controller의 password는 다음의 경로에서 확인 가능하다.
# $ vi {PaaS-TA-Project}/cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_cf_db_password.png)

```text
-v cc_db_password="sd4ttnf384dfkculs616"
-v cc_driver_name="mysql"                         # cloud controller의 database driver를 지정한다.
-v uaa_db_id="uaa"                                # uaa database의 유저명을 지정한다.

# uaa_db_password : uaa의 db password 이며 PaaS-TA Core(CF)의 uaa가 배포가 되어 있어야 한다.
# uaa database의 password는 다음의 경로에서 확인 가능하다.
# $ vi {PaaS-TA-Project}/cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_uaa_db_password.png)

```text
-v uaa_db_password="2n93lzsqlixn9vds3w73"
-v uaa_driver_name="mysql"                        # uaa 의 database driver를 지정한다.

# cf_uaa_url : 표기되는 IP주소는 PaaS-TA Core(CF) deployment시 정의된 system_domain의 IP 주소와 동일하다.
-v cf_uaa_url="https://uaa.192.168.x.x.xip.io"
-v cf_uaa_logouturl="logout.do"                   # cf uaa 로그아웃시의 url을 지정한다.

# cf_api_url : 표기되는 IP주소는 PaaS-TA Core(CF) deployment의 정의된 system_domain의 IP 주소와 동일하다.
-v cf_api_url=https://api.192.168.x.x.xip.io

# cf_admin_password : cf의 admin password 이며 PaaS-TA Core(CF) 가 배포가 되어 있어야 한다.
# cf의 admin password는 다음의 경로에서 확인 가능하다.
# $ vi {PaaS-TA-Project}cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_cf_admin_password%20%281%29.png)

```text
-v cf_admin_password="oh8zwobgd1uxxsi3zq7c"

# uaa_admin_client_secret : cf의 uaa admin secret 이며 PaaS-TA Core(CF)의 uaa가 배포가 되어 있어야 한다.
# cf의 uaa admin client secret은 다음의 경로에서 확인 가능하다.
# $ vi {PaaS-TA-Project}/cf-kubernetes/creds.yml
```

![](../../.gitbook/assets/kubernetes_verify_uaa_admin_password.png)

```text
-v uaa_admin_client_secret=”44sf9e5ss2dldqg17xx”
# portal_client_secet의 값은 2.5.4.3. uaa portalclient 생성 참고한다.
-v portal_client_secret="portalsecret"

# paas_ta_web_user_url :  상기 옵션 중 haproxy_public_ip과 동일한 IP 주소를 입력한다.
-v paas_ta_web_user_url="http://portal-web-user.192.168.150.28.xip.io"
-v abacus_url="http://abacus.192.168.x.x"                  # 현재는 사용하지 않음. 임의의 값을 입력한다. 업데이트 예정  
-v portal_webuser_quantity=false                           # portal webuser quantity. Default false.
-v monitoring_api_url="http://monitoring.192.168.x.x"      # 현재는 사용하지 않음. 임의의 값을 입력한다. 업데이트 예정
-v portal_webuser_monitoring=false                         # portal webuser monitoring. Default false.
-v mail_smtp_host="smtp.gmail.com"                         # 메일 전송 서버를 지정한다.
-v mail_smtp_port="465"                                    # 메일 전송 서버의 Port를 지정한다.
-v mail_smtp_username="PaaS-TA"                            # 메일 전송 서버의 유저 명을 지정한다.
-v mail_smtp_password="smtppassword"                       # 메일 전송 서버의 패스워드를 지정한다.
-v mail_smtp_useremail="openpasta@gmail.com"               # 메일 전송 서버의 유저 메일을 지정한다.
-v mail_smtp_properties_starttls_enable="true"             # 메일 전송 시 TLS 활성 유무를 지정한다.
-v mail_smtp_properties_auth="true"                        # 메일 전송 시 인증 유무를 지정한다.
-v mail_smtp_properties_starttls_required="true"           # 메일 전송 시 TLS 필수 값 유무를 지정한다.
-v portal_webuser_automaticapproval=false                   
-v mail_smtp_properties_subject="PaaS-TA User Potal"       # 메일 전송 시 제목을 지정한다.
-v infra_admin=false
```

스크립트에 대한 실행 권한을 부여한다.

```text
$ chmod 755 paasta-portal-deploy-kubernetes.sh
```

1.2. PaaS-TA 사용자 포털을 배포한다.

```text
$ cd ~/workspace/{PaaS-TA-Project}
```

PaaS-TA 사용자 포털 배포 스크립트를 수행한다.

```text
$ ./paasta-portal-deploy-kubernetes.sh
```

PaaS-TA 사용자 포털 배포를 실행하고 배포 진행 과정에 대해 정상적으로 배포가 수행되는지 로그를 필히 확인한다.

**2.    Cloudit 로드밸런싱 설정**

2.1. PaaS-TA 사용자 포털 배포 이후 사용자 및 운영자 포털을 접속하기 위해 HAProxy가 배포된 VM을 확인한다.  
\# bosh를 통해 배포되어 사용중인 VM을 확인한다.

```text
$ bosh -e paasta -d paasta-portal vms
```

\# HAProxy가 배포된 inctance의 VM CID를 참고한다. ![](../../.gitbook/assets/kubernetes_verify_haproxy_instance_id.png)

아래 명령어를 통해 위에서 얻은 VM CID 값으로 haproxy가 어느 Worker node에 배포되었는지 확인한다. 아래 예제의 경우는 NODE 컬럼을 통해 paasta-4 node에 haproxy가 배포 되어 있는 것을 확인할 수 있다.

```text
$ kubectl get pod -o wide -n paasta | grep vm-4a925467-3b98-4d19-7950-62fdbac337e7
```

![](../../.gitbook/assets/kubernetes_verify_haproxy_instance_id_1.png)

2.2. Cloudit 로드밸런서 생성에 따라 로드밸런싱 설정을 확인한다. ※ [2.1.5 Cloudit 로드밸런서 생성 – HAProxy 배포 VM](paas-ta_platform_install_automation_cloudit_portal_v1.0.md#23)

※ 로드밸런싱 설정 시 다음의 정보를 따른다. 1. 로드밸런서의 IP는 PaaS-TA 사용자 포털 배포시 사용했던 haproxy\_public\_ip의 IP주소를 입력한다. 2. 로드밸런서 구성 요소 중 서버IP 항목의 물리 VM은 haproxy가 배포된 VM을 지정한다. 3. 로드밸런서 구성 요소 중 멤버 항목의 서버포트에 대한 정보는 portal-proxy-ingress 항목을 참고한다. 80\(proxy\)과 2221\(eureka\)에 대한 nodePort를 지정한다.

ingress 정보 확인  
\# 80과 2221 포트와 매핑 된 NodePort에 대해 로드밸런싱 포트를 추가한다.  
\# 아래 예제에선 80:30602/TCP의 내용과 2221:30702/TCP의 내용이 여기에 해당된다.

```text
    $ kubectl get service portal-proxy-ingress -n paasta
```

![](../../.gitbook/assets/kubernetes_verify_proxy_ingress.png)

**3.    최종 포털 사용을 위한 Service 확인**

3.1. 등록된 Service를 확인한다. 아래 명령어를 통해 등록된 Service가 다음과 같은지 확인한다.

```text
$ kubectl get services -n paasta -o wide
```

![](../../.gitbook/assets/kubernetes_verify_svc.png)

**4.    PaaS-TA 사용자 포털 접속**

4.1. PaaS-TA 사용자 포털에 접속한다. 아래 명령어를 통해 배포된 PaaS-TA 사용자 포털에 접속한다. PaaS-TA 사용자 포털 배포 후 등록했던 로드밸런서 IP를 입력하거나 도메인 주소를 입력한다. Public IP와 매핑이 되어 있다면, 브라우저를 통해 Public IP로 접속을 한다

```text
$ curl http://portal-web-user.192.168.x.x.xip.io
```

![](../../.gitbook/assets/kubernetes_verify_portal_connection.png)

**5.    PaaS-TA 운영자 포털 접속**

5.1. PaaS-TA 운영자 포털에 접속한다. 아래 명령어를 통해 배포된 PaaS-TA 운영자 포털에 접속한다. PaaS-TA 사용자 포털 배포 후 등록했던 로드밸런서 IP를 입력한다. Public IP와 매핑이 되어 있다면, 브라우저를 통해 Public IP로 접속을 한다

```text
$ curl http://portal-web-admin.192.168.x.x.xip.io
```

![](../../.gitbook/assets/kubernetes_verify_admin_connection.png)

**6.    관리용 Eureka 접속**

6.1. Eureka web에 접속한다. 아래 명령어를 통해 Eureka web에 접속한다. PaaS-TA 사용자 포털 배포 후 등록했던 로드밸런서 IP를 입력한다. Public IP와 매핑이 되어 있다면, 브라우저를 통해 Public IP로 접속을 한다

```text
$ curl 192.168.x.x:2221
```

![](../../.gitbook/assets/kubernetes_verify_eureka_connection.png)

![](../../.gitbook/assets/kubernetes_verify_eureka_url.png)

위와 같이 Application 목록의 URL이 표시되어야 한다.

