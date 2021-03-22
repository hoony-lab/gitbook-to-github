# PAAS-TA\_PORTAL\_UI\_SERVICE\_INSTALL\_GUIDE\_V1.0

### Table of Contents

1. [문서 개요](paas-ta_portal_ui_service_install_guide_v1.0.md#1) 1.1. [목적](paas-ta_portal_ui_service_install_guide_v1.0.md#1.1) 1.2. [범위](paas-ta_portal_ui_service_install_guide_v1.0.md#1.2) 1.3. [시스템 구성도](paas-ta_portal_ui_service_install_guide_v1.0.md#1.3) 1.4. [참고자료](paas-ta_portal_ui_service_install_guide_v1.0.md#1.4)
2. [PaaS-TA Portal 설치](paas-ta_portal_ui_service_install_guide_v1.0.md#2) 2.1. [Prerequisite](paas-ta_portal_ui_service_install_guide_v1.0.md#2.1) 2.2. [Stemcell 확인](paas-ta_portal_ui_service_install_guide_v1.0.md#2.2) 2.3. [Deployment 다운로드](paas-ta_portal_ui_service_install_guide_v1.0.md#2.3) 2.4. [Deployment 파일 수정](paas-ta_portal_ui_service_install_guide_v1.0.md#2.4) 2.5. [서비스 설치](paas-ta_portal_ui_service_install_guide_v1.0.md#2.5) 2.6. [서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식](paas-ta_portal_ui_service_install_guide_v1.0.md#2.6) 2.7. [서비스 설치 확인](paas-ta_portal_ui_service_install_guide_v1.0.md#2.7) 2.8. [Portal SSH 설치](paas-ta_portal_ui_service_install_guide_v1.0.md#2.8)
3. [PaaS-TA Portal 운영](paas-ta_portal_ui_service_install_guide_v1.0.md#3) 3.1. [사용자의 조직 생성 Flag 활성화](paas-ta_portal_ui_service_install_guide_v1.0.md#3.1) 3.2. [사용자포탈 UAA 페이지 오류](paas-ta_portal_ui_service_install_guide_v1.0.md#3.2) 3.3. [운영자포탈 유저 페이지 조회 오류](paas-ta_portal_ui_service_install_guide_v1.0.md#3.3) 3.4. [Log](paas-ta_portal_ui_service_install_guide_v1.0.md#3.4) 3.5. [카탈로그 적용](paas-ta_portal_ui_service_install_guide_v1.0.md#3.5) 3.6. [모니터링 및 오토스케일링 적용](paas-ta_portal_ui_service_install_guide_v1.0.md#3.6)

### 1. 문서 개요

#### 1.1. 목적

본 문서\(PaaS-TA Portal Release 설치 가이드\)는 전자정부표준프레임워크 기반의 PaaS-TA에서 제공되는 PaaS-TA Portal Release를 Bosh2.0을 이용하여 설치 하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 내부 네트워크는 link를 적용시켜 자동으로 Ip가 할당이 된다. 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

#### 1.2. 범위

설치 범위는 PaaS-TA Portal Release를 검증하기 위한 기본 설치를 기준으로 작성하였다.

#### 1.3. 시스템 구성도

본 문서의 설치된 시스템 구성도이다. Binary Storage, Mariadb, Proxy, Gateway Api, Registration Api, Portal Api, Common Api, Log Api, Storage Api, Webadmin, Webuser로 최소사항을 구성하였다.

![](../../.gitbook/assets/Paas-TA-Portal_01.png)

* Paas-TA Portal 설치할때 cloud config에 추가적으로 정의한 VM\_Tpye명과 스펙 

| VM\_Type | 스펙 |
| :--- | :--- |
| portal\_tiny | 1vCPU / 256MB RAM / 4GB Disk |
| portal\_medium | 1vCPU / 1GB RAM / 4GB Disk |
| portal\_small | 1vCPU / 512MB RAM / 4GB Disk |

* Paas-TA Portal각 Instance의 Resource Pool과 스펙

| 구분 | Resource Pool | 스펙 |
| :--- | :--- | :--- |
| haproxy | portal\_small | 1vCPU / 512MB RAM / 4GB Disk |
| mariadb | portal\_small | 1vCPU / 512MB RAM / 4GB Disk +10GB\(영구적 Disk\) |
| paas-ta-portal-webadmin | portal\_small | 1vCPU / 512MB RAM / 4GB Disk |
| paas-ta-portal-webuser | portal\_small | 1vCPU / 512MB RAM / 4GB Disk |

#### 1.4. 참고자료

[**http://bosh.io/docs**](http://bosh.io/docs)  
[**http://docs.cloudfoundry.org/**](http://docs.cloudfoundry.org/)

### 2. PaaS-TA Portal 설치

#### 2.1. Prerequisite

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스팩 설치를 위해서는 먼저 BOSH CLI v2 가 설치 되어 있어야 하고 BOSH 에 로그인이 되어 있어야 한다.  
 BOSH CLI v2 가 설치 되어 있지 않을 경우 먼저 BOSH2.0 설치 가이드 문서를 참고 하여 BOSH CLI v2를 설치를 하고 사용법을 숙지 해야 한다.  


* BOSH2.0 사용자 가이드
  * [BOSH2 사용자 가이드](../bosh/paas-ta_bosh2_install_guide_v5.0.md) 
  * [BOSH CLI V2 사용자 가이드](https://github.com/PaaS-TA/Guide-4.0-ROTELLE/blob/master/Use-Guide/Bosh/PaaS-TA_BOSH_CLI_V2_사용자_가이드v1.0.md)

#### 2.2. Stemcell 확인

Stemcell 목록을 확인하여 서비스 설치에 필요한 Stemcell이 업로드 되어 있는 것을 확인한다. \(PaaS-TA 5.5.1 과 동일 stemcell 사용\)

> $ bosh -e ${BOSH\_ENVIRONMENT} stemcells

```text
Using environment '10.0.1.6' as client 'admin'

Name                                     Version  OS             CPI  CID  
bosh-aws-xen-hvm-ubuntu-xenial-go_agent  621.94*  ubuntu-xenial  -    ami-038de43e4d9b6f5fb  

(*) Currently deployed

1 stemcells

Succeeded
```

#### 2.3. Deployment 다운로드

서비스 설치에 필요한 Deployment를 Git Repository에서 받아 서비스 설치 작업 경로로 위치시킨다.

* Portal Deployment Git Repository URL : [https://github.com/PaaS-TA/portal-deployment/tree/v5.1.1](https://github.com/PaaS-TA/portal-deployment/tree/v5.1.1)

```text
# Deployment 다운로드 파일 위치 경로 생성 및 설치 경로 이동
$ mkdir -p ~/workspace/paasta-5.5.1/deployment
$ cd ~/workspace/paasta-5.5.1/deployment

# Deployment 파일 다운로드
$ git clone https://github.com/PaaS-TA/portal-deployment.git -b v5.1.1
```

#### 2.4. Deployment 파일 수정

BOSH Deployment manifest는 Components 요소 및 배포의 속성을 정의한 YAML 파일이다. Deployment 파일에서 사용하는 network, vm\_type, disk\_type 등은 Cloud config를 활용하고, 활용 방법은 BOSH 2.0 가이드를 참고한다.

* Cloud config 설정 내용을 확인한다.   

> $ bosh -e ${BOSH\_ENVIRONMENT} cloud-config

```text
Using environment '10.0.1.6' as client 'admin'

azs:
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z1
- cloud_properties:
    availability_zone: ap-northeast-2a
  name: z2

... ((생략)) ...

disk_types:
- disk_size: 1024
  name: default
- disk_size: 1024
  name: 1GB

... ((생략)) ...

networks:
- name: default
  subnets:
  - az: z1
    cloud_properties:
      security_groups: paasta-security-group
      subnet: subnet-00000000000000000
    dns:
    - 8.8.8.8
    gateway: 10.0.1.1
    range: 10.0.1.0/24
    reserved:
    - 10.0.1.2 - 10.0.1.9
    static:
    - 10.0.1.10 - 10.0.1.120

... ((생략)) ...

vm_types:
- cloud_properties:
    ephemeral_disk:
      size: 3000
      type: gp2
    instance_type: t2.small
  name: minimal
- cloud_properties:
    ephemeral_disk:
      size: 10000
      type: gp2
    instance_type: t2.small
  name: small

... ((생략)) ...

Succeeded
```

* common\_vars.yml을 서버 환경에 맞게 수정한다. 
* Portal-UI에서 사용하는 변수는 system\_domain, paasta\_api\_version, uaa\_client\_portal\_secret 이다.

> $ vi ~/workspace/paasta-5.5.1/deployment/common/common\_vars.yml
>
> \`\`\`
>
> ## BOSH INFO
>
> bosh\_ip: "10.0.1.6" \# BOSH IP bosh\_url: "[https://10.0.1.6](https://10.0.1.6)" \# BOSH URL \(e.g. "[https://00.000.0.0](https://00.000.0.0)"\) bosh\_client\_admin\_id: "admin" \# BOSH Client Admin ID bosh\_client\_admin\_secret: "ert7na4jpew" \# BOSH Client Admin Secret\('echo $\(bosh int ~/workspace/paasta-5.5.1/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin\_password\)' 명령어를 통해 확인 가능\) bosh\_director\_port: 25555 \# BOSH director port bosh\_oauth\_port: 8443 \# BOSH oauth port bosh\_version: 271.2 \# BOSH version\('bosh env' 명령어를 통해 확인 가능, on-demand service용, e.g. "271.2"\)

## PAAS-TA INFO

system\_domain: "61.252.53.246.xip.io" \# Domain \(xip.io를 사용하는 경우 HAProxy Public IP와 동일\) paasta\_admin\_username: "admin" \# PaaS-TA Admin Username paasta\_admin\_password: "admin" \# PaaS-TA Admin Password paasta\_nats\_ip: "10.0.1.121" paasta\_nats\_port: 4222 paasta\_nats\_user: "nats" paasta\_nats\_password: "7EZB5ZkMLMqT7" \# PaaS-TA Nats Password \(CredHub 로그인후 'credhub get -n /micro-bosh/paasta/nats\_password' 명령어를 통해 확인 가능\) paasta\_nats\_private\_networks\_name: "default" \# PaaS-TA Nats 의 Network 이름 paasta\_database\_ips: "10.0.1.123" \# PaaS-TA Database IP \(e.g. "10.0.1.123"\) paasta\_database\_port: 5524 \# PaaS-TA Database Port \(e.g. 5524\(postgresql\)/13307\(mysql\)\) -- Do Not Use "3306"&"13306" in mysql paasta\_database\_type: "postgresql" \# PaaS-TA Database Type \(e.g. "postgresql" or "mysql"\) paasta\_database\_driver\_class: "org.postgresql.Driver" \# PaaS-TA Database driver-class \(e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver"\) paasta\_cc\_db\_id: "cloud\_controller" \# CCDB ID \(e.g. "cloud\_controller"\) paasta\_cc\_db\_password: "cc\_admin" \# CCDB Password \(e.g. "cc\_admin"\) paasta\_uaa\_db\_id: "uaa" \# UAADB ID \(e.g. "uaa"\) paasta\_uaa\_db\_password: "uaa\_admin" \# UAADB Password \(e.g. "uaa\_admin"\) paasta\_api\_version: "v3"

## UAAC INFO

uaa\_client\_admin\_id: "admin" \# UAAC Admin Client Admin ID uaa\_client\_admin\_secret: "admin-secret" \# UAAC Admin Client에 접근하기 위한 Secret 변수 uaa\_client\_portal\_secret: "clientsecret" \# UAAC Portal Client에 접근하기 위한 Secret 변수

## Monitoring INFO

metric\_url: "10.0.161.101" \# Monitoring InfluxDB IP syslog\_address: "10.0.121.100" \# Logsearch의 ls-router IP syslog\_port: "2514" \# Logsearch의 ls-router Port syslog\_transport: "relp" \# Logsearch Protocol saas\_monitoring\_url: "61.252.53.248" \# Pinpoint HAProxy WEBUI의 Public IP monitoring\_api\_url: "61.252.53.241" \# Monitoring-WEB의 Public IP

#### Portal INFO

portal\_web\_user\_ip: "52.78.88.252" portal\_web\_user\_url: "[http://portal-web-user.52.78.88.252.xip.io](http://portal-web-user.52.78.88.252.xip.io)"

#### ETC INFO

abacus\_url: "[http://abacus.61.252.53.248.xip.io](http://abacus.61.252.53.248.xip.io)" \# abacus url \(e.g. "[http://abacus.xxx.xxx.xxx.xxx.xip.io](http://abacus.xxx.xxx.xxx.xxx.xip.io)"\)

```text
- Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/portal-deployment/portal-ui/vars.yml
```

## STEMCELL INFO

stemcell\_os: "ubuntu-xenial" \# stemcell os stemcell\_version: "621.94" \# stemcell version

## NETWORKS INFO

private\_networks\_name: "default" \# private network name public\_networks\_name: "vip" \# public network name

## MARIADB INFO

mariadb\_azs: \[z6\] \# mariadb : azs mariadb\_instances: 1 \# mariadb : instances \(1\) mariadb\_vm\_type: "minimal" \# mariadb : vm type mariadb\_persistent\_disk\_type: "10GB" \# mariadb : persistent disk type mariadb\_port: "" \# mariadb : database port \(e.g. 13306\) -- Do Not Use "3306" mariadb\_admin\_password: "" \# mariadb : database admin password \(e.g. "mariadb"\)

## HAPROXY INFO

haproxy\_azs: \[z7\] \# haproxy : azs haproxy\_instances: 1 \# haproxy : instances \(1\) haproxy\_vm\_type: "small" \# haproxy : vm type haproxy\_public\_ips: "" \# haproxy : public ips \(e.g. "00.00.00.00"\) haproxy\_infra\_admin: false \# haproxy : infra admin \(default "false"\)

## PORTAL\_WEBADMIN INFO

webadmin\_azs: \[z6\] \# webadmin : azs webadmin\_instances: 1 \# webadmin : instances \(1\) webadmin\_vm\_type: "small" \# webadmin : vm type

## PORTAL\_WEBUSER INFO

webuser\_azs: \[z6\] \# webuser : azs webuser\_instances: 1 \# webuser : instances \(1\) webuser\_vm\_type: "small" \# webuser : vm type webuser\_monitoring: false \# webuser : monitoring 사용 여부. true일 경우 앱 상세정보에서 모니터링창이 활성화 된다. webuser\_quantity: false \# webuser : 사용량 조회 창 활성화 여부 webuser\_automaticapproval: false \# webuser : 회원가입시 PaaS-TA에 접속 가능 여부. true일 경우 관리자 포탈에서 승인을 해주어야 접근이 가능하다. user\_app\_size: 0 \# webuser : 사용자 myApp 배포시 용량제한 여부 \(값이 0일경우 무제한\)

## ETC INFO

portal\_default\_api\_name: "PaaS-TA 5.5.1" \# ETC : default api name portal\_default\_api\_url: "http://:2225" \# ETC : default api url portal\_default\_header\_auth: "Basic YWRtaW46b3BlbnBhYXN0YQ==" \# ETC : default header auth portal\_default\_api\_desc: "PaaS-TA 5.5.1 install infra" \# ETC : default api description

```text
### <div id="2.5"/> 2.5. 서비스 설치

- 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.  
     (선택) -o operations/use-compiled-releases.yml (ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용)  

> $ vi ~/workspace/paasta-5.5.1/deployment/portal-deployment/portal-ui/deploy.sh
```

## !/bin/bash

## VARIABLES

COMMON\_VARS\_PATH="" \# common\_vars.yml File Path \(e.g. ../../common/common\_vars.yml\) CURRENT\_IAAS="${CURRENT\_IAAS}" \# IaaS Information \(PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력\) BOSH\_ENVIRONMENT="${BOSH\_ENVIRONMENT}" \# bosh director alias name \(PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력\)

## DEPLOY

bosh -e ${BOSH\_ENVIRONMENT} -n -d portal-ui deploy portal-ui.yml  -o operations/${CURRENT\_IAAS}-network.yml  -l ${COMMON\_VARS\_PATH}  -l vars.yml

```text
- 서비스를 설치한다.
```

$ cd ~/workspace/paasta-5.5.1/deployment/portal-deployment/portal-ui  
$ sh ./deploy.sh

```text
### <div id="2.6"/> 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식

- 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.  

  - 설치 릴리즈 파일 다운로드 : [paasta-portal-ui-release-2.4.0.tgz](https://nextcloud.paas-ta.org/index.php/s/sEF75LCgqCXgF4s/download)
```

## 릴리즈 다운로드 파일 위치 경로 생성

$ mkdir -p ~/workspace/paasta-5.5.1/release/portal

## 릴리즈 파일 다운로드 및 파일 경로 확인

$ ls ~/workspace/paasta-5.5.1/release/portal paasta-portal-ui-release-2.4.0.tgz

```text
- 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.  
     (추가) -o operations/use-offline-releases.yml (미리 다운받은 offline 릴리즈 사용)  
     (추가) -v releases_dir="<RELEASE_DIRECTORY>"  

> $ vi ~/workspace/paasta-5.5.1/deployment/portal-deployment/portal-ui/deploy.sh
```

## !/bin/bash

## VARIABLES

COMMON\_VARS\_PATH="" \# common\_vars.yml File Path \(e.g. ../../common/common\_vars.yml\) CURRENT\_IAAS="${CURRENT\_IAAS}" \# IaaS Information \(PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력\) BOSH\_ENVIRONMENT="${BOSH\_ENVIRONMENT}" \# bosh director alias name \(PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력\)

## DEPLOY

bosh -e ${BOSH\_ENVIRONMENT} -n -d portal-ui deploy portal-ui.yml  -o operations/use-offline-releases.yml  -o operations/${CURRENT\_IAAS}-network.yml  -l ${COMMON\_VARS\_PATH}  -l vars.yml  -v releases\_dir="/home/ubuntu/workspace/paasta-5.5.1/release"

```text
- 서비스를 설치한다.
```

$ cd ~/workspace/paasta-5.5.1/deployment/portal-deployment/portal-ui  
$ sh ./deploy.sh

```text
### <div id="2.7"/> 2.7. 서비스 설치 확인

설치 완료된 서비스를 확인한다.  

> $ bosh -e ${BOSH_ENVIRONMENT} -d portal-ui vms
```

Using environment '10.0.1.6' as client 'admin'

Task 4823. Done

Deployment 'portal-ui'

Instance Process State AZ IPs VM CID VM Type Active  
haproxy/5c30c643-94d1-491c-9f6c-e72de4b0e6a4 running z7 10.30.56.10 vm-891ff2dd-4ee0-4c42-8fa8-b2d0cf0b8537 portal\_tiny true  
115.68.46.180  
mariadb/19bf81a9-cde9-432b-87ca-cbac1f28854a running z6 10.30.56.9 vm-7a6f8042-e9b8-434c-abbf-776bbfd3386d portal\_small true  
paas-ta-portal-webadmin/bc536f61-10bd-4702-af5f-5e63500e110e running z6 10.30.56.11 vm-176ccac5-f154-4420-b821-9ed30a18f3e2 portal\_small true  
paas-ta-portal-webuser/409c038b-d013-41d3-b6b2-aebb4a02d908 running z6 10.30.56.12 vm-d9cf481f-64c7-45fd-aadb-e4eb1b31945a portal\_tiny true

4 vms

Succeeded

```text
### <div id="2.8"/> 2.8. Portal SSH 설치

Portal 5.1.0 버전 이상부터는 배포된 어플리케이션의 SSH 접속이 가능하다.

이를 위해 Portal SSH App을 먼저 배포해야 한다.

- Portal 배포를 위한 조직 및 공간 생성
```

#### Portal 배포를 위한 조직 및 공간 생성 및 설정

$ cf create-quota portal\_quota -m 20G -i -1 -s -1 -r -1 --reserved-route-ports -1 --allow-paid-service-plans $ cf create-org portal -q portal\_quota $ cf create-space system -o portal  
$ cf target -o portal -s system

```text
- Portal SSH 다운로드 및 배포
```

$ cd ~/workspace/paasta-5.5.1/release/portal $ wget --content-disposition [https://nextcloud.paas-ta.org/index.php/s/awPjYDYCMiHY7yF/download](https://nextcloud.paas-ta.org/index.php/s/awPjYDYCMiHY7yF/download) $ unzip portal-ssh.zip $ cd portal-ssh $ cf push

```text
## <div id="3"/>3. PaaS-TA Portal 운영

### <div id="3.1"/> 3.1. 사용자의 조직 생성 Flag 활성화 

PaaS-TA는 기본적으로 일반 사용자는 조직을 생성할 수 없도록 설정되어 있다. 포털 배포를 위해 조직 및 공간을 생성해야 하고 또 테스트를 구동하기 위해서도 필요하므로 사용자가 조직을 생성할 수 있도록 user_org_creation FLAG를 활성화 한다. FLAG 활성화를 위해서는 PaaS-TA 운영자 계정으로 로그인이 필요하다.
```

$ cf enable-feature-flag user\_org\_creation

```text

```

Setting status of user\_org\_creation as admin... OK

Feature user\_org\_creation Enabled.

\`\`\`

#### 3.2. 사용자포탈 UAA페이지 오류

> ![](../../.gitbook/assets/Paas-TA-Portal_27.jpg) 1. uaac portalclient가 등록이 되어있지 않다면 해당 화면과 같이 redirect오류가 발생한다. 2. uaac client add를 통해 potalclient를 추가시켜주어야 한다. $ uaac target $ uaac token client get Client ID: admin Client secret: **\***

1. uaac client add portalclient –s “portalclient Secret”

   > --redirect\_uri "사용자포탈 Url, 사용자포탈 Url/callback" $ uaac client add portalclient -s xxxxx --redirect\_uri "[http://portal-web-user.xxxx.xip.io](http://portal-web-user.xxxx.xip.io), [http://portal-web-user.xxxx.xip.io/callback](http://portal-web-user.xxxx.xip.io/callback)"  --scope "cloud\_controller\_service\_permissions.read , openid , cloud\_controller.read , cloud\_controller.write , cloud\_controller.admin"  --authorized\_grant\_types "authorization\_code , client\_credentials , refresh\_token"  --authorities="uaa.resource"  --autoapprove="openid , cloud\_controller\_service\_permissions.read"
   >
   > ![](../../.gitbook/assets/Paas-TA-Portal_28.jpg)

2. uaac portalclient가 url이 잘못 등록되어있다면 해당 화면과 같이 redirect오류가 발생한다. 
3. uaac client update를 통해 url을 수정해야한다.

   > $ uaac target $ uaac token client get Client ID: admin Client secret: **\***

4. uaac client update portalclient --redirect\_uri "사용자포탈 Url, 사용자포탈 Url/callback"

   > $ uaac client update portalclient --redirect\_uri "[http://portal-web-user.xxxx.xip.io](http://portal-web-user.xxxx.xip.io), [http://portal-web-user.xxxx.xip.io/callback](http://portal-web-user.xxxx.xip.io/callback)"

#### 3.3. 운영자 포탈 유저 페이지 조회 오류

1. 페이지 이동시 정보를 가져오지 못하고 오류가 났을 경우 common-api VM으로 이동후에 DB 정보 config를 수정후 재시작을 해 주어야 한다.

#### 3.4. Log

Paas-TA Portal 각각 Instance의 log를 확인 할 수 있다. 1. 로그를 확인할 Instance에 접근한다.

> bosh ssh -d \[deployment name\] \[instance name\]

```text
   Instance                                                          Process State  AZ  IPs            VM CID                                   VM Type        Active   
   haproxy/8cc2d633-2b43-4f3d-a2e8-72f5279c11d5                      running        z5  10.30.107.213  vm-315bfa1b-9829-46de-a19d-3bd65e9f9ad4  portal_large   true  
                                                                                        115.68.46.214                                                            
   mariadb/117cbf05-b223-4133-bf61-e15f16494e21                      running        z5  10.30.107.211  vm-bc5ae334-12d4-41d4-8411-d9315a96a305  portal_large   true  
   paas-ta-portal-webadmin/8047fcbd-9a98-4b61-b161-0cbb277fa643      running        z5  10.30.107.221  vm-188250fd-e918-4aab-9cbe-7d368852ea8a  portal_medium  true  
   paas-ta-portal-webuser/cb206717-81c9-49ed-a0a8-e6c3b957cb66       running        z5  10.30.107.222  vm-822f68a5-91c8-453a-b9b3-c1bbb388e377  portal_medium  true  

   11 vms

   Succeeded
   inception@inception:~$ bosh ssh -d paas-ta-portal-ui paas-ta-portal-webadmin  << instance 접근(bosh ssh) 명령어 입력
   Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)

   Using deployment 'paas-ta-portal-webadmin'

   Task 5195. Done
   Unauthorized use is strictly prohibited. All access and activity
   is subject to logging and monitoring.
   Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 4.4.0-92-generic x86_64)

    * Documentation:  https://help.ubuntu.com/

   The programs included with the Ubuntu system are free software;
   the exact distribution terms for each program are described in the
   individual files in /usr/share/doc/*/copyright.

   Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
   applicable law.

   Last login: Tue Sep  4 07:11:42 2018 from 10.30.20.28
   To run a command as administrator (user "root"), use "sudo <command>".
   See "man sudo_root" for details.

   paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$ 
```

1. 로그파일이 있는 폴더로 이동한다.

   > 위치 : /var/vcap/sys/log/\[job name\]/

   ```text
     paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:~$ cd /var/vcap/sys/log/paas-ta-portal-webadmin/
     paas-ta-portal-webadmin/48fa0c5a-52eb-4ae8-a7b9-91275615318c:/var/vcap/sys/log/paas-ta-portal-webadmin$ ls
     paas-ta-portal-webadmin.stderr.log  paas-ta-portal-webadmin.stdout.log
   ```

2. 로그파일을 열어 내용을 확인한다.

   > vim \[job name\].stdout.log

   ```text
    예)
    vim paas-ta-portal-webadmin.stdout.log
    2018-09-04 02:08:42.447 ERROR 7268 --- [nio-2222-exec-1] p.p.a.e.GlobalControllerExceptionHandler : Error message : Response : org.springframework.security.web.firewall.FirewalledResponse@298a1dc2
    Occured an exception : 403 Access token denied.
    Caused by...
    org.cloudfoundry.client.lib.CloudFoundryException: 403 Access token denied. (error="access_denied", error_description="Access token denied.")
            at org.cloudfoundry.client.lib.oauth2.OauthClient.createToken(OauthClient.java:114)
            at org.cloudfoundry.client.lib.oauth2.OauthClient.init(OauthClient.java:70)
            at org.cloudfoundry.client.lib.rest.CloudControllerClientImpl.initialize(CloudControllerClientImpl.java:187)
            at org.cloudfoundry.client.lib.rest.CloudControllerClientImpl.<init>(CloudControllerClientImpl.java:163)
            at org.cloudfoundry.client.lib.rest.CloudControllerClientFactory.newCloudController(CloudControllerClientFactory.java:69)
            at org.cloudfoundry.client.lib.CloudFoundryClient.<init>(CloudFoundryClient.java:138)
            at org.cloudfoundry.client.lib.CloudFoundryClient.<init>(CloudFoundryClient.java:102)
            at org.openpaas.paasta.portal.api.service.LoginService.login(LoginService.java:47)
            at org.openpaas.paasta.portal.api.controller.LoginController.login(LoginController.java:51)
            at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
            at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
            at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
            at java.lang.reflect.Method.invoke(Method.java:498)
            at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
            at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:133)
            at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:97)
            at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:827)
            at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:738)
            at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:85)
            at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:967)
            at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:901)
            at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:970)
            at org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:872)
            at javax.servlet.http.HttpServlet.service(HttpServlet.java:661)
            at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
            at javax.servlet.http.HttpServlet.service(HttpServlet.java:742)
            at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
            at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
            at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
            at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
            at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
   ```

#### 3.5. 카탈로그 적용

**1. Catalog 빌드팩, 서비스팩 추가**

Paas-TA Portal 설치 후에 관리자 포탈에서 빌드팩, 서비스팩을 등록해야 사용자 포탈에서 사용이 가능하다.

1. 관리자 포탈에 접속한다.\(portal-web-admin.\[public ip\].xip.io\)

   > ![](../../.gitbook/assets/Paas-TA-Portal_15.png)

2. 운영관리를 누른다.

   > ![](../../.gitbook/assets/Paas-TA-Portal_16%20%281%29.png)

3. 카탈로그 페이지에 들어간다.

   > ![](../../.gitbook/assets/Paas-TA-Portal_17.png)

4. 빌드팩, 서비스팩 상세화면에 들어가서 각 항목란에 값을 입력후에 저장을 누른다.

   > ![](../../.gitbook/assets/Paas-TA-Portal_18%20%281%29%20%281%29.png)

5. 사용자포탈에서 변경된값이 적용되어있는지 확인한다.

   > ![](../../.gitbook/assets/Paas-TA-Portal_19%20%281%29%20%281%29%20%281%29.png)

#### 3.6. 모니터링 및 오토스케일링 적용

**1. 포탈 설치 이전 모니터링 설정 적용**

**1.PaaS-TA 에서 제공하고있는 모니터링을 미리 설치를 한 후에 진행해야 한다.**

1. Paas-TA Portal 설치 시 공통 변수 파일과 Deployment 변수 파일의 monitoring\_api\_url=&lt;모니터링 API URL&gt;, webuser\_monitoring=true로 적용 한 후 설치 하면 정상적으로 모니터링 페이지 및 오토스케일링을 사용 할 수 있다.

**2. 포탈 설치 이후 모니터링 설정 적용**

1. 사용자 포탈의 앱 상세 페이지로 이동한다.

   > ![](../../.gitbook/assets/Paas-TA-Portal_30.png)

2. ① 상세페이지 레이아웃 하단의 모니터링 버튼을 누른다.
3. ② 모니터링 오토 스케일링 화면
4. ③ 모니터링 알람 설정 화면
5. 추이차트 탭에서 디스크 메모리 네트워크 사용량을 인스턴스 별로 확인이 가능하다.

