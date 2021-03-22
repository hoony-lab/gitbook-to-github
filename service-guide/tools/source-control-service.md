# Source Control Service 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](source-control-service.md#1) 1.1. [목적](source-control-service.md#1.1) 1.2. [범위](source-control-service.md#1.2) 1.3. [시스템 구성도](source-control-service.md#1.3) 1.4. [참고 자료](source-control-service.md#1.4)​
2. 3. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서는 개방형 PaaS 플랫폼 고도화 및 개발자 지원 환경 기반의 Open PaaS에서 제공되는 서비스인 형상관리 서비스를 Bosh를 이용하여 설치하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 형상관리 서비스 Release를 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

본 장에서는 형상관리 서비스의 시스템 구성에 대해 기술하였다. 형상관리 서비스의 시스템은 service-broker, mariadb, 형상관리 Server의 최소 사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F68cad5c5a6b49f373e5820fc822c80b6009443e5.PNG?alt=media)

시스템 구성도

### 1.4. 참고 자료 <a id="1-4"></a>

> ​[http://bosh.io/docs](http://bosh.io/docs) [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)​

## 2. 형상관리 서비스 설치 <a id="2"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0 이상, PaaS-TA 포털이 설치되어 있어야 한다.

### 2.2. Stemcell 확인 <a id="2-2-stemcell"></a>

Stemcell 목록을 확인하여 서비스 설치에 필요한 Stemcell이 업로드 되어 있는 것을 확인한다. \(PaaS-TA 5.5.1 과 동일 stemcell 사용\)

> $ bosh -e micro-bosh stemcells

```text
Using environment '10.0.1.6' as client 'admin'​Name                                     Version  OS             CPI  CID  bosh-aws-xen-hvm-ubuntu-xenial-go_agent  621.94*  ubuntu-xenial  -    ami-0297ff649e8eea21b  ​(*) Currently deployed​1 stemcells​Succeeded
```

### 2.3. Deployment 다운로드 <a id="2-3-deployment"></a>

서비스 설치에 필요한 Deployment를 Git Repository에서 받아 서비스 설치 작업 경로로 위치시킨다.

* 
```text
# Deployment 다운로드 파일 위치 경로 생성 및 설치 경로 이동$ mkdir -p ~/workspace/paasta-5.5.1/deployment$ cd ~/workspace/paasta-5.5.1/deployment​# Deployment 파일 다운로드$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.0.6​# common_vars.yml 파일 다운로드(common_vars.yml가 존재하지 않는다면 다운로드)$ git clone https://github.com/PaaS-TA/common.git
```

### 2.4. Deployment 파일 수정 <a id="2-4-deployment"></a>

BOSH Deployment manifest는 Components 요소 및 배포의 속성을 정의한 YAML 파일이다. Deployment 파일에서 사용하는 network, vm\_type, disk\_type 등은 Cloud config를 활용하고, 활용 방법은 BOSH 2.0 가이드를 참고한다.

* Cloud config 설정 내용을 확인한다.

> $ bosh -e micro-bosh cloud-config

```text
Using environment '10.0.1.6' as client 'admin'​azs:- cloud_properties:    availability_zone: ap-northeast-2a  name: z1- cloud_properties:    availability_zone: ap-northeast-2a  name: z2​... ((생략)) ...​disk_types:- disk_size: 1024  name: default- disk_size: 1024  name: 1GB​... ((생략)) ...​networks:- name: default  subnets:  - az: z1    cloud_properties:      security_groups: paasta-security-group      subnet: subnet-00000000000000000    dns:    - 8.8.8.8    gateway: 10.0.1.1    range: 10.0.1.0/24    reserved:    - 10.0.1.2 - 10.0.1.9    static:    - 10.0.1.10 - 10.0.1.120​... ((생략)) ...​vm_types:- cloud_properties:    ephemeral_disk:      size: 3000      type: gp2    instance_type: t2.small  name: minimal- cloud_properties:    ephemeral_disk:      size: 10000      type: gp2    instance_type: t2.small  name: small​... ((생략)) ...​Succeeded
```

* common\_vars.yml을 서버 환경에 맞게 수정한다.
* 형상관리 서비스에서 사용하는 변수는 system\_domain이다.

> $ vi ~/workspace/paasta-5.5.1/deployment/common/common\_vars.yml

```text
# BOSH INFObosh_ip: "10.0.1.6"                # BOSH IPbosh_url: "https://10.0.1.6"            # BOSH URL (e.g. "https://00.000.0.0")bosh_client_admin_id: "admin"            # BOSH Client Admin IDbosh_client_admin_secret: "ert7na4jpew48"    # BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-5.5.1/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' 명령어를 통해 확인 가능)bosh_director_port: 25555            # BOSH director portbosh_oauth_port: 8443                # BOSH oauth portbosh_version: 271.2                # BOSH version('bosh env' 명령어를 통해 확인 가능, on-demand service용, e.g. "271.2")​# PAAS-TA INFOsystem_domain: "61.252.53.246.xip.io"        # Domain (xip.io를 사용하는 경우 HAProxy Public IP와 동일)paasta_admin_username: "admin"            # PaaS-TA Admin Usernamepaasta_admin_password: "admin"            # PaaS-TA Admin Passwordpaasta_nats_ip: "10.0.1.121"paasta_nats_port: 4222paasta_nats_user: "nats"paasta_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"    # PaaS-TA Nats Password (CredHub 로그인후 'credhub get -n /micro-bosh/paasta/nats_password' 명령어를 통해 확인 가능)paasta_nats_private_networks_name: "default"    # PaaS-TA Nats 의 Network 이름paasta_database_ips: "10.0.1.123"        # PaaS-TA Database IP (e.g. "10.0.1.123")paasta_database_port: 5524            # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysqlpaasta_database_type: "postgresql"                      # PaaS-TA Database Type (e.g. "postgresql" or "mysql")paasta_database_driver_class: "org.postgresql.Driver"   # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")paasta_cc_db_id: "cloud_controller"        # CCDB ID (e.g. "cloud_controller")paasta_cc_db_password: "cc_admin"        # CCDB Password (e.g. "cc_admin")paasta_uaa_db_id: "uaa"                # UAADB ID (e.g. "uaa")paasta_uaa_db_password: "uaa_admin"        # UAADB Password (e.g. "uaa_admin")paasta_api_version: "v3"​# UAAC INFOuaa_client_admin_id: "admin"            # UAAC Admin Client Admin IDuaa_client_admin_secret: "admin-secret"        # UAAC Admin Client에 접근하기 위한 Secret 변수uaa_client_portal_secret: "clientsecret"    # UAAC Portal Client에 접근하기 위한 Secret 변수​# Monitoring INFOmetric_url: "10.0.161.101"            # Monitoring InfluxDB IPsyslog_address: "10.0.121.100"                # Logsearch의 ls-router IPsyslog_port: "2514"                              # Logsearch의 ls-router Portsyslog_transport: "relp"                        # Logsearch Protocolsaas_monitoring_url: "61.252.53.248"           # Pinpoint HAProxy WEBUI의 Public IPmonitoring_api_url: "61.252.53.241"            # Monitoring-WEB의 Public IP​### Portal INFOportal_web_user_ip: "52.78.88.252"portal_web_user_url: "http://portal-web-user.52.78.88.252.xip.io" ​### ETC INFOabacus_url: "http://abacus.61.252.53.248.xip.io"    # abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.xip.io")
```

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/source-control-service/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"                                   # stemcell osstemcell_version: "621.94"                                     # stemcell version​# VM_TYPEvm_type_small: "minimal"                                       # vm type small​# NETWORKprivate_networks_name: "default"                               # private network namepublic_networks_name: "vip"                                    # public network name​# SCM-SERVERscm_azs: [z3]                                                  # scm : azsscm_instances: 1                                               # scm : instances (1)scm_persistent_disk_type: "30GB"                               # scm : persistent disk typescm_private_ips: ""                           # scm : private ips (e.g. "10.0.81.41")​# MARIA-DB# MARIA_DBmariadb_azs: [z3]                                              # mariadb : azsmariadb_instances: 1                                           # mariadb : instances (1) mariadb_persistent_disk_type: "2GB"                            # mariadb : persistent disk type mariadb_private_ips: ""                   # mariadb : private ips (e.g. "10.0.81.42")mariadb_port: ""                                 # mariadb : database port (e.g. 31306) -- Do Not Use "3306"mariadb_admin_password: ""             # mariadb : database admin password (e.g. "!paas_ta202")mariadb_broker_username: ""           # mariadb : service-broker-user id (e.g. "sourcecontrol")mariadb_broker_password: ""           # mariadb : service-broker-user password (e.g. "!scadmin2017")​# HAPROXYhaproxy_azs: [z7]                                              # haproxy : azshaproxy_instances: 1                                           # haproxy : instances (1)haproxy_persistent_disk_type: "2GB"                            # haproxy : persistent disk typehaproxy_private_ips: ""                   # haproxy : private ips (e.g. "10.0.0.31")haproxy_public_ips: ""                     # haproxy : public ips (e.g. "101.101.101.5")​# WEB-UIweb_ui_azs: [z3]                                               # web-ui : azsweb_ui_instances: 1                                            # web-ui : instances (1)web_ui_persistent_disk_type: "2GB"                             # web-ui : persistent disk typeweb_ui_private_ips: ""                     # web-ui : private ips (e.g. "10.0.81.44")​# SCM-APIapi_azs: [z3]                                                  # scm-api : azsapi_instances: 1                                               # scm-api : instances (1)api_persistent_disk_type: "2GB"                                # scm-api : persistent disk typeapi_private_ips: ""                           # scm-api : private ips (e.g. "10.0.81.45")​# SERVICE-BROKERbroker_azs: [z3]                                               # service-broker : azsbroker_instances: 1                                            # service-broker : instances (1)broker_persistent_disk_type: "2GB"                             # service-broker : persistent disk typebroker_private_ips: ""                     # service-broker : private ips (e.g. "10.0.81.46")​# UAACuaa_client_sc_id: "scclient"                                   # source-control-service uaa client iduaa_client_sc_secret: "clientsecret"                           # source-control-service uaa client secret
```

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/source-control-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d source-control-service deploy --no-redact source-control-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/source-control-service  $ sh ./deploy.sh
```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
* 
```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaasta-sourcecontrol-release-1.0.1.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/source-control-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d source-control-service deploy --no-redact source-control-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/source-control-service   $ sh ./deploy.sh
```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d source-control-service vms

```text
Using environment '10.0.1.6' as client 'admin'​Task 7847. Done​Deployment 'source-control-service'​Instance                                                   Process State  AZ  IPs            VM CID                                   VM Type  Activehaproxy/878b393c-d817-4e71-8fe5-553ddf87d362               running        z5  115.68.47.179  vm-cc4c774d-9857-4b3a-9ffc-5f836098eb4e  minimal  true                                              10.30.107.123mariadb/90ea7861-57ed-43f7-853d-25712a67ba2a               running        z5  10.30.107.122  vm-6952fb76-b4d6-4f53-86cb-b0517a76f0d0  minimal  truescm-server/6e23addc-33c7-4bb0-af95-d66420f15c06            running        z5  10.30.107.121  vm-08eb6dd3-04ae-435c-9558-9b78286b730c  minimal  truesourcecontrol-api/3ecb90fa-2211-4df6-82bb-5c91ed9a4310     running        z5  10.30.107.125  vm-ee4daa28-3e10-4409-9d6f-ce566d54e8a5  minimal  truesourcecontrol-broker/ec83edb5-130f-4a91-9ac1-20fb622ed0a2  running        z5  10.30.107.126  vm-23d1a9fc-30d2-4a0f-8631-df8807fc8612  minimal  truesourcecontrol-webui/840278e2-e1a2-4a30-b904-68538c7cd06f   running        z5  10.30.107.124  vm-0f7300dd-63b5-4399-b1fd-35aeccffac5c  minimal  true​6 vms​Succeeded
```

## 3. 형상관리 서비스 관리 및 신청 <a id="3"></a>

### 3.1. 서비스 브로커 등록 <a id="3-1"></a>

서비스의 설치가 완료 되면, PaaS-TA 포탈에서 서비스를 사용하기 위해 형상관리 서비스 브로커를 등록해 주어야 한다. 서비스 브로커 등록 시에는 개방형 클라우드 플랫폼에서 서비스 브로커를 등록 할 수 있는 권한을 가진 사용자로 로그인 되어 있어야 한다.

* 서비스 브로커 목록을 확인한다

  > $ cf service-brokers

```text
Getting service brokers as admin...​name   urlNo service brokers found
```

* 형상관리 서비스 브로커를 등록한다.

  > $ cf create-service-broker \[SERVICE\_BROKER\] \[USERNAME\] \[PASSWORD\] \[SERVICE\_BROKER\_URL\]
  >
  > * \[SERVICE\_BROKER\] : 서비스 브로커 명
  > * \[USERNAME\] / \[PASSWORD\] : 서비스 브로커에 접근할 수 있는 사용자 ID / PASSWORD
  > * \[SERVICE\_BROKER\_URL\] : 서비스 브로커 접근 URL

```text
### e.g. 형상관리 서비스 브로커 등록$ cf create-service-broker paasta-sourcecontrol-broker admin cloudfoundry http://10.30.107.126:8080Creating service broker paasta-sourcecontrol-broker as admin...   OK
```

* 등록된 형상관리 서비스 브로커를 확인한다.

  > $ cf service-brokers

```text
Getting service brokers as admin...​name                         urlpaasta-sourcecontrol-broker   http://10.30.107.126:8080
```

* 형상관리 서비스의 서비스 접근 정보를 확인한다.

  > $ cf service-access -b paasta-sourcecontrol-broker

```text
Getting service access for broker paasta-sourcecontrol-broker as admin...broker: paasta-sourcecontrol-broker   service                  plan      access   orgs   p-paasta-sourcecontrol   Default   none
```

* 형상관리 서비스의 서비스 접근 허용을 설정\(전체\)하고 서비스 접근 정보를 재확인 한다.

  > $ cf enable-service-access p-paasta-sourcecontrol $ cf service-access -b paasta-sourcecontrol-broker

```text
$ cf enable-service-access p-paasta-sourcecontrolEnabling access to all plans of service p-paasta-sourcecontrol for all orgs as admin...OK​$ cf service-access -b paasta-sourcecontrol-broker Getting service access for broker paasta-sourcecontrol-broker as admin...broker: paasta-sourcecontrol-broker   service                  plan      access   orgs   p-paasta-sourcecontrol   Default   all
```

### 3.2. UAA Client 등록 <a id="3-2-uaa-client"></a>

* uaac server의 endpoint를 설정한다.

```text
# endpoint 설정$ uaac target https://uaa. --skip-ssl-validation​# target 확인$ uaac targetTarget: https://uaa.Context: uaa_admin, from client uaa_admin
```

* uaac 로그인을 한다.

```text
$ uaac token client get  -s Successfully fetched token via client credentials grant.Target: https://uaa.Context: admin, from client admin
```

* 형상관리 서비스 계정을 생성 한다. $ uaac client add -s --redirect\_uri &lt;형상관리서비스 대시보드 URI&gt; --scope &lt;퍼미션 범위&gt; --authorized\_grant\_types &lt;권한 타입&gt; --authorities=&lt;권한 퍼미션&gt; --autoapprove=&lt;자동승인권한&gt;
  *  : uaac 클라이언트 id
  *  : uaac 클라이언트 secret
  * &lt;형상관리서비스 대시보드 URI&gt; : 성공적으로 리다이렉션 할 형상관리서비스 대시보드 URI

    \("http://:8080, http://:8080/repositories, http://:8080/repositories/user"\)

  * &lt;퍼미션 범위&gt; : 클라이언트가 사용자를 대신하여 얻을 수있는 허용 범위 목록
  * &lt;권한 타입&gt; : 서비스가 제공하는 API를 사용할 수 있는 권한 목록
  * &lt;권한 퍼미션&gt; : 클라이언트에 부여 된 권한 목록
  * &lt;자동승인권한&gt; : 사용자 승인이 필요하지 않은 권한 목록

```text
# e.g. 형상관리 서비스 계정 생성$ uaac client add scclient -s clientsecret --redirect_uri "http://115.68.47.179:8080 http://115.68.47.179:8080/repositories http://115.68.47.179:8080/repositories/user" \  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \  --authorities="uaa.resource" \  --autoapprove="openid , cloud_controller_service_permissions.read"​# e.g. 형상관리 서비스 계정 생성 확인$ uaac clientsscclient    scope: cloud_controller.read cloud_controller.write cloud_controller_service_permissions.read openid        cloud_controller.admin    resource_ids: none    authorized_grant_types: refresh_token client_credentials authorization_code    redirect_uri: http://115.68.47.179:8080 http://115.68.47.179:8080/repositories http://115.68.47.179:8080/repositories/user    autoapprove: cloud_controller_service_permissions.read openid    authorities: uaa.resource    name: scclient    lastmodified: 1542894096080
```

### 3.3. 서비스 신청 <a id="3-3"></a>

* 형상관리 서비스 사용을 위해 서비스를 신청 한다.

> $ cf create-service \[SERVICE\] \[PLAN\] \[SERVICE\_INSTANCE\] \[-c PARAMETERS\_AS\_JSON\]
>
> * \[SERVICE\] / \[PLAN\] : 서비스 명과 서비스 플랜
> * \[SERVICE\_INSTANCE\] : 서비스 인스턴스 명 \(내 서비스 목록에서 보여지는 명칭\)
> * \[-c PARAMETERS\_AS\_JSON\] : JSON 형식의 파라미터 \(형상관리 서비스 신청 시, owner, org\_name 파라미터는 필수\)

```text
### e.g. 형상관리 서비스 신청$ cf create-service p-paasta-sourcecontrol Default paasta-sourcecontrol -c '{"owner":"demo", "org_name":"demo"}'  service                  plans                                   description  paasta-sourcecontrol     Default                                 A paasta source control service for application development.provision parameters : parameters {owner : {owner}, org_name : {org_name}}  paasta-sourcecontrol-broker​### e.g. 형상관리 서비스 확인$ cf servicesGetting services in org system / space dev as admin...​name                    service                  plan            bound apps   last operation     broker                        upgrade availablepaasta-sourcecontrol    p-paasta-sourcecontrol   Default                      create succeeded   paasta-sourcecontrol-broker
```

* 서비스 상세의 대시보드 URL 정보를 확인하여 서비스에 접근한다.

  ```text
  ### 서비스 상세 정보의 Dashboard URL을 확인한다.$ cf service paasta-sourcecontrol... (생략) ...Dashboard:        http://115.68.47.179:8080/repositories/user/b840ecb4-15fb-4b35-a9fc-185f42f0de37Service broker:   paasta-sourcecontrol-broker... (생략) ...
  ```

