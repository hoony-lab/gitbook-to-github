# 형상관리 서비스 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](undefined.md#1) 1.1. [목적](undefined.md#1.1) 1.2. [범위](undefined.md#1.2) 1.3. [시스템 구성도](undefined.md#1.3) 1.4. [참고자료](undefined.md#1.4)​
2. 3. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서\(배포 파이프라인 서비스팩 설치 가이드\)는 개방형 PaaS 플랫폼 고도화 및 개발자 지원 환경 기반의 Open PaaS에서 제공되는 서비스팩인 배포 파이프라인 서비스팩을 Bosh를 이용하여 설치 및 서비스 등록하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 배포 파이프라인 서비스팩을 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

본 문서의 설치된 시스템 구성도이다. 배포 파이프라인 Server, 형상관리 서비스 브로커로 최소사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff4114d58c071866ff4f04b0071cbfb8eccb4f258.jpg?alt=media)

시스템 구성도

### 1.4. 참고 자료 <a id="1-4"></a>

> ​[http://bosh.io/docs](http://bosh.io/docs) [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)​

## 2. 배포 파이프라인 서비스 설치 <a id="2"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스팩 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0 이상, PaaS-TA 포털이 설치되어 있어야 한다.

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
* 배포 파이프라인에서 사용하는 변수는 system\_domain 이다.

> $ vi ~/workspace/paasta-5.5.1/deployment/common/common\_vars.yml

```text
# BOSH INFObosh_ip: "10.0.1.6"                # BOSH IPbosh_url: "https://10.0.1.6"            # BOSH URL (e.g. "https://00.000.0.0")bosh_client_admin_id: "admin"            # BOSH Client Admin IDbosh_client_admin_secret: "ert7na4jpew48"    # BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-5.5.1/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' 명령어를 통해 확인 가능)bosh_director_port: 25555            # BOSH director portbosh_oauth_port: 8443                # BOSH oauth portbosh_version: 271.2                # BOSH version('bosh env' 명령어를 통해 확인 가능, on-demand service용, e.g. "271.2")​# PAAS-TA INFOsystem_domain: "61.252.53.246.xip.io"        # Domain (xip.io를 사용하는 경우 HAProxy Public IP와 동일)paasta_admin_username: "admin"            # PaaS-TA Admin Usernamepaasta_admin_password: "admin"            # PaaS-TA Admin Passwordpaasta_nats_ip: "10.0.1.121"paasta_nats_port: 4222paasta_nats_user: "nats"paasta_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"    # PaaS-TA Nats Password (CredHub 로그인후 'credhub get -n /micro-bosh/paasta/nats_password' 명령어를 통해 확인 가능)paasta_nats_private_networks_name: "default"    # PaaS-TA Nats 의 Network 이름paasta_database_ips: "10.0.1.123"        # PaaS-TA Database IP (e.g. "10.0.1.123")paasta_database_port: 5524            # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysqlpaasta_database_type: "postgresql"                      # PaaS-TA Database Type (e.g. "postgresql" or "mysql")paasta_database_driver_class: "org.postgresql.Driver"   # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")paasta_cc_db_id: "cloud_controller"        # CCDB ID (e.g. "cloud_controller")paasta_cc_db_password: "cc_admin"        # CCDB Password (e.g. "cc_admin")paasta_uaa_db_id: "uaa"                # UAADB ID (e.g. "uaa")paasta_uaa_db_password: "uaa_admin"        # UAADB Password (e.g. "uaa_admin")paasta_api_version: "v3"​# UAAC INFOuaa_client_admin_id: "admin"            # UAAC Admin Client Admin IDuaa_client_admin_secret: "admin-secret"        # UAAC Admin Client에 접근하기 위한 Secret 변수uaa_client_portal_secret: "clientsecret"    # UAAC Portal Client에 접근하기 위한 Secret 변수​# Monitoring INFOmetric_url: "10.0.161.101"            # Monitoring InfluxDB IPsyslog_address: "10.0.121.100"                # Logsearch의 ls-router IPsyslog_port: "2514"                              # Logsearch의 ls-router Portsyslog_transport: "relp"                        # Logsearch Protocolsaas_monitoring_url: "61.252.53.248"           # Pinpoint HAProxy WEBUI의 Public IPmonitoring_api_url: "61.252.53.241"            # Monitoring-WEB의 Public IP​### Portal INFOportal_web_user_ip: "52.78.88.252"portal_web_user_url: "http://portal-web-user.52.78.88.252.xip.io" ​### ETC INFOabacus_url: "http://abacus.61.252.53.248.xip.io"    # abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.xip.io")
```

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/pipeline-service/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"                                     # stemcell osstemcell_version: "621.94"                                       # stemcell version​# NETWORKprivate_networks_name: "default"                                 # private network namepublic_networks_name: "vip"                                      # public network name​# UAACpipeline_clinet_id: "pipeclient"                                 # pipeline client id for UAApipeline_clinet_secret: "clientsecret"                           # pipeline client password for UAA​# MARIADBmariadb_port: "13306"                                            # mariadb database port (default : 13306) -- Do Not Use "3306"mariadb_azs: [z5]                                                # mariadb azsmariadb_instances: 1                                             # mariadb instancesmariadb_persistent_disk_type: "2GB"                              # mariadb persistent disk typemariadb_vm_type: "small"                                         # mariadb vm type (e.g. small/medium/large etc)mariadb_internal_static_ips: ""              # mariadb's private IP (e.g. "10.0.161.30")mariadb_admin_password: ""               # mariadb admin password (e.g. "admin!Service")​# POSTGRESpostgres_port: "5532"                                            # postgresql port (default : 5532) -- Do Not Use "5432"postgres_azs: [z5]                                               # postgresql azspostgres_instances: 1                                            # postgresql instancespostgres_persistent_disk_type: "2GB"                             # postgresql persistent disk typepostgres_vm_type: "small"                                        # postgresql vm typepostgres_internal_static_ips: ""            # postgresql's private IP (e.g. "10.0.161.31")postgres_datasource_username: ""        # postgresql username (e.g. sonar)postgres_datasource_password: ""        # postgresql password (e.g. [email protected])​# INSPECTION_SERVERinspection_azs: [z5]                                             # inspection server(SonarQube) azsinspection_instances: 1                                          # inspection server(SonarQube) instances inspection_vm_type: "small"                                      # inspection server(SonarQube) vm typeinspection_internal_static_ips: "" # inspection server(SonarQube)'s private IP (e.g. "10.0.161.32")​# HAPROXYhaproxy_azs: [z7]                                                # haproxy azshaproxy_instances: 1                                             # haproxy instanceshaproxy_vm_type: "small"                                         # haproxy vm typehaproxy_internal_static_ips: ""              # haproxy's private IP (e.g. "10.0.0.11")haproxy_public_static_ips: ""                 # haproxy's public IP​# CI_SERVERci_server_azs: [z5]                                                           # ci server(Jenkins) azsci_server_instances: 2                                                        # ci server(Jenkins) instancesci_server_persistent_disk_type: "5GB"                                         # ci server(Jenkins) persistent disk typeci_server_vm_type: "small"                                                    # ci server(Jenkins) vm typeci_server_shared_internal_static_ip: ""           # ci server(Jenkins)'s private IP for shared (e.g. "10.0.161.33")ci_server_dedicated_internal_static_ip: ""    # ci server(Jenkins)'s public IP for dedicated (e.g. "10.0.161.34")ci_server_password: ""                                    # ci server(Jenkins) password (e.g. "[email protected]#")ci_server_admin_user_username: ""                   # ci server(Jenkins) admin username (e.g. "admin")ci_server_admin_user_password: ""                   # ci server(Jenkins) admin password (e.g. "[email protected]#")ci_server_http_url: ""                                    # ci server(Jenkins) 내부 IP 앞 두자리 입력 (e.g. 10.110.10.10 의 경우, "10.110" 입력)​# BINARY_STORAGEbinary_storage_azs: [z5]                                           # binary storage azsbinary_storage_instances: 1                                        # binary storage instancesbinary_storage_persistent_disk_type: "5GB"                         # binary storage persistent disk typebinary_storage_vm_type: "small"                                    # binary storage vm typebinary_storage_internal_static_ips: ""  # binary storage's private IP (e.g. "10.0.161.35")binary_storage_proxy_port: "10008"                                 # binary storage 프록시 서버 Port(Object Storage 접속 Port) (default : 10008)binary_storage_auth_port: 15001                                    # binary storage keystone port (e.g. 15001) -- Do Not Use "5000"binary_storage_username: "paasta-pipeline"                         # binary storage 최초 생성되는 유저이름(Object Storage 접속 유저이름)binary_storage_password: "paasta-pipeline"                         # binary storage 최초 생성되는 유저 비밀번호(Object Storage 접속 유저 비밀번호)binary_storage_tenantname: "paasta-pipeline"                       # binary storage 최초 생성되는 테넌트 이름(Object Storage 접속 테넌트 이름)binary_storage_email: "[email protected]"                            # binary storage 최소 생성되는 유저의 이메일binary_storage_binary_desc: "paasta-pipeline-object service"       # binary storage 설명binary_storage_container: "delivery-pipeline-container"            # binary storage 최소 생성되는container 이름​# COMMON_APIcommon_api_port: "8081"                                          # common api port common_api_azs: [z5]                                             # common api azscommon_api_instances: 1                                          # common api instancescommon_api_vm_type: "small"                                      # common api vm typecommon_api_internal_static_ips: ""        # common api's private IP (e.g. "10.0.161.36")​# INSPECTION_APIinspection_api_port: "8083"                                         # inspection api portinspection_api_azs: [z5]                                            # inspection api azsinspection_api_instances: 1                                         # inspection api instancesinspection_api_vm_type: "small"                                     # inspection api vm typeinspection_api_internal_static_ips: ""   # inspection api's private IP (e.g. "10.0.161.37")​# BINARY_STORAGE_APIstorage_api_port: "8080"                                         # storage api portstorage_api_azs: [z5]                                            # storage api azsstorage_api_instances: 1                                         # storage api instancesstorage_api_vm_type: "small"                                     # storage api vm typestorage_api_internal_static_ips: ""      # storage api's private IP (e.g. "10.0.161.38")​# APIapi_port: "8082"                                                 # api port api_azs: [z5]                                                    # api azsapi_instances: 1                                                 # api instancesapi_persistent_disk_type: "2GB"                                  # api persistent disk typeapi_vm_type: "small"                                             # api vm typeapi_internal_static_ips: ""                      # api's private IP (e.g. "10.0.161.39")​# SERVICE_BROKERservice_broker_port: "8080"                                       # pipeline service broker portservice_broker_azs: [z5]                                          # pipeline service broker azsservice_broker_instances: 1                                       # pipeline service broker instancesservice_broker_persistent_disk_type: "2GB"                        # pipeline service broker persistent disk typeservice_broker_vm_type: "small"                                   # pipeline service broker vm typeservice_broker_internal_static_ips: "" # pipeline service broker's private IP (e.g. "10.0.161.40")​# UI(DASHBOARD)ui_port: "8084"                                                  # ui(dahsboard) portui_azs: [z5]                                                     # ui(dahsboard) azsui_instances: 1                                                  # ui(dahsboard) instancesui_vm_type: "small"                                              # ui(dahsboard) vm typeui_internal_static_ips: ""                        # ui(dahsboard)'s private IP (e.g. "10.0.161.41")​# SCHEDULERscheduler_port: "8080"                                           # scheduler portscheduler_azs: [z5]                                              # scheduler azsscheduler_instances: 1                                           # scheduler instancesscheduler_vm_type: "small"                                       # scheduler vm typescheduler_internal_static_ips: ""          # scheduler's private IP (e.g. "10.0.161.42")
```

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/pipeline-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d pipeline-service deploy --no-redact pipeline-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml
```

* 서비스를 설치한다.

  ```text
  $ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/pipeline-service  $ sh ./deploy.sh
  ```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
  * 

```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드(paasta-delivery-pipeline-release.tgz) 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaasta-delivery-pipeline-release-1.0.2.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/pipeline-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d pipeline-service deploy --no-redact pipeline-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

  ```text
  $ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/pipeline-service  $ sh ./deploy.sh
  ```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 된 서비스를 확인한다.

> $ bosh -e micro-bosh -d pipeline-service vms

```text
Using environment '10.0.1.6' as client 'admin'​Task 296077. Done​Deployment 'pipeline-service'​Instance                                                                   Process State  AZ  IPs            VM CID                                VM Type  Active  Stemcell  binary_storage/63b0c3de-0037-46c7-add7-c7fe54a9ac6c                        running        z5  10.0.161.17    28b7e75b-6fb4-4e3a-8a90-68d20e934441  small    true    -  ci_server/48d7ffb1-9ac2-42af-915c-9adc7a215656                             running        z5  10.0.161.15    9694f782-acc2-4958-946c-1a21010c6325  small    true    -  ci_server/de71d530-6b95-482e-a419-32e3c1e64f21                             running        z5  10.0.161.16    c7858b61-2dc1-4878-9e21-5ff5cd07d47b  small    true    -  delivery-pipeline-api/ea2486ff-6477-4899-9e95-7370ed27efbe                 running        z5  10.0.161.21    ca7c0346-b086-4948-9da1-5410c5eec778  small    true    -  delivery-pipeline-binary-storage-api/9d70363c-7e42-4d67-a452-b50a21a4e373  running        z5  10.0.161.20    fbc6441e-73e1-41c8-a5f4-c5fc00336936  small    true    -  delivery-pipeline-common-api/87ce092c-2fc6-4b1b-9921-78a73e191c9e          running        z5  10.0.161.18    23e8e141-c869-449c-873e-f48a49252521  small    true    -  delivery-pipeline-inspection-api/b8f8d86a-443a-482e-a668-b94624a882fb      running        z5  10.0.161.19    88425e31-68a2-42cd-9431-5a7cc372b9e6  small    true    -  delivery-pipeline-scheduler/d2c02e3c-e545-47e1-a98a-d81de281a166           running        z5  10.0.161.24    8401e44a-16d5-4639-990c-876655500773  small    true    -  delivery-pipeline-service-broker/151af074-db1f-4650-b789-477e69c51016      running        z5  10.0.161.22    d1f474ed-48cf-43e3-b7e8-a4cb05c00dbf  small    true    -  delivery-pipeline-ui/d3ff6d00-93b9-4481-ac07-66cea65322f9                  running        z5  10.0.161.23    c65f56c5-13f8-4c12-8726-295a384b0a63  small    true    -  haproxy/5a35c6b2-ac18-45cd-9705-a7a401721989                               running        z5  10.0.161.14    a1d70ef6-064d-46b1-b8fc-83ffb08ad82f  small    true    -                                                                                                101.55.50.208                                                           inspection/0a91abe1-b888-4f86-a082-efd6aa9936de                            running        z5  10.0.161.13    5c7a1f2e-b406-44d2-b5fe-2f694c36036c  small    true    -  mariadb/521553a6-4145-4c5c-9d8f-475db29c5807                               running        z5  10.0.161.11    5476fe5d-a4b2-4b25-8db7-00a0afa30186  small    true    -  postgres/6a8a4d71-e46f-49ca-b992-407441a90965                              running        z5  10.0.161.12    c87ffcd0-599e-4f07-8d03-3b52d7ae3762  small    true    -  ​14 vms​Succeeded
```

## 3. 배포 파이프라인 서비스 관리 및 신청 <a id="3"></a>

PaaS-TA 운영자 포탈을 통해 배포파이프라인 서비스를 등록 및 공개하면, PaaS-TA 사용자 포탈을 통해 서비스를 신청 하여 사용할 수 있다.

### 3.1. 서비스 브로커 등록 <a id="3-1"></a>

배포 파이프라인 서비스팩 배포가 완료되었으면 파스-타 포탈에서 서비스 팩을 사용하기 위해서 먼저 배포 파이프라인 서비스 브로커를 등록해 주어야 한다. 서비스 브로커 등록 시 개방형 클라우드 플랫폼에서 서비스 브로커를 등록할 수 있는 사용자로 로그인이 되어있어야 한다.

#### 서비스 브로커 목록을 확인한다. <a id="undefined"></a>

> $ cf service-brokers

```text
Getting service brokers as admin...​name   urlNo service brokers found
```

#### 배포 파이프라인 서비스 브로커를 등록한다. <a id="undefined-1"></a>

> $ cf create-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 사용자비밀번호} [http://{서비스팩](http://xn--{-bl3fx6e30bf82b/) URL}

**서비스팩 이름** : 서비스 팩 관리를 위해 PaaS-TA에서 보여지는 명칭이다. 서비스 Marketplace에서는 각각의 API 서비스 명이 보여지니 여기서 명칭은 서비스팩 리스트의 명칭이다. **서비스팩 사용자ID** / 비밀번호 : 서비스팩에 접근할 수 있는 사용자 ID입니다. 서비스팩도 하나의 API 서버이기 때문에 아무나 접근을 허용할 수 없어 접근이 가능한 ID/비밀번호를 입력한다. **서비스팩 URL** : 서비스팩이 제공하는 API를 사용할 수 있는 URL을 입력한다.

> $ cf create-service-broker delivery-pipeline admin cloudfoundry [http://10.30.107.64:8080](http://10.30.107.64:8080/)​

```text
Creating service broker delivery-pipeline-broker as admin...OK
```

#### 등록된 배포 파이프라인 서비스 브로커를 확인한다. <a id="undefined-2"></a>

> $ cf service-brokers

```text
Getting service brokers as admin...​name                           urldelivery-pipeline-broker       http://10.30.107.64:8080
```

#### 접근 가능한 서비스 목록을 확인한다. <a id="undefined-3"></a>

> $ cf service-access

```text
# 서비스 브로커 생성시 디폴트로 접근을 허용하지 않는다.$ cf service-accessGetting service access as admin...broker: delivery-pipeline-broker   service             plan                          access   orgs   delivery-pipeline   delivery-pipeline-shared      none   delivery-pipeline   delivery-pipeline-dedicated   none
```

#### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. \(전체 조직\) <a id="undefined-4"></a>

> $ cf enable-service-access delivery-pipeline $ cf service-access

```text
$ cf enable-service-access delivery-pipeline                                         Enabling access to all plans of service delivery-pipeline for all orgs as admin...   OK​$ cf service-accessGetting service access as admin...broker: delivery-pipeline-broker   service             plan                          access   orgs   delivery-pipeline   delivery-pipeline-shared      all   delivery-pipeline   delivery-pipeline-dedicated   all
```

### 3.2. UAAC Client 등록 <a id="3-2-uaac-client"></a>

UAAC Client 계정 등록 절차에 대한 순서를 확인한다.

* 배포 파이프라인 UAAC Client를 등록한다.

  > $ uaac client add {클라이언트 명} -s {클라이언트 비밀번호} --redirect\_URL{대시보드 URL} --scope {퍼미션 범위} --authorized\_grant\_types {권한 타입} --authorities={권한 퍼미션} --autoapprove={자동승인권한} 클라이언트 명 : uaac 클라이언트 명 \(pipeclient\) 클라이언트 비밀번호 : uaac 클라이언트 비밀번호 대시보드 URL: 성공적으로 리다이렉션 할 대시보드 URL 퍼미션 범위: 클라이언트가 사용자를 대신하여 얻을 수있는 허용 범위 목록 권한 타입 : 서비스팩이 제공하는 API를 사용할 수 있는 권한 목록 권한 퍼미션 : 클라이언트에 부여 된 권한 목록 자동승인권한: 사용자 승인이 필요하지 않은 권한 목록

> $ uaac client add pipeclient -s clientsecret --redirect\_uri "\[DASHBOARD\_URL\]" / --scope "cloud\_controller\_service\_permissions.read , openid , cloud\_controller.read , cloud\_controller.write , cloud\_controller.admin" / --authorized\_grant\_types "authorization\_code , client\_credentials , refresh\_token" / --authorities="uaa.resource" / --autoapprove="openid , cloud\_controller\_service\_permissions.read"

```text
### uaac endpoint 설정$ uaac target https://uaa. --skip-ssl-validation​### target 확인$ uaac targetTarget: https://uaa.Context: uaa_admin, from client uaa_admin​### uaac 로그인$ uaac token client get  -s ​### 배포파이프라인 uaac client 등록$ uaac client add pipeclient -s clientsecret --redirect_uri "http://115.68.47.175:8084 http://115.68.47.175:8084/dashboard" \   --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \   --authorized_grant_types "authorization_code , client_credentials , refresh_token" \   --authorities="uaa.resource" \   --autoapprove="openid , cloud_controller_service_permissions.read"
```

### 3.3. Java Offline Buildpack 등록 <a id="3-3-java-offline-buildpack"></a>

* 배포 파이프라인 서비스 사용을 위해 Java Offline Buildpack을 등록한다.

  > $ cf create-buildpack \[BUILDPACK\] \[PATH\] \[POSITION\] **\[BUILDPACK\]** : java\_buildpack\_offline \(buildpack 명\) **\[PATH\]** : buildpack zip 파일의 경로 **\[POSITION\]** : 우선순위

* Java Offline Buildpack 다운로드

  > wget -O java-buildpack-offline-v4.25.zip [http://45.248.73.44/index.php/s/mcaBZQCqwbyzC6a/download](http://45.248.73.44/index.php/s/mcaBZQCqwbyzC6a/download)​

**buildpack 등록**

> $ cf create-buildpack java\_buildpack\_offline ..\buildpack\java-buildpack-offline-v4.25.zip 3

**buildpack 등록 확인**

> $ cf buildpacks

```text
Getting buildpacks...​buildpack                position   enabled   locked   filenamestaticfile_buildpack     1          true      false    staticfile_buildpack-cflinuxfs3-v1.4.43.zipjava_buildpack           2          true      false    java-buildpack-cflinuxfs3-v4.19.1.zipjava_buildpack_offline   3          true      false    java-buildpack-offline-v4.25.zipruby_buildpack           4          true      false    ruby_buildpack-cflinuxfs3-v1.7.40.zipdotnet_core_buildpack    5          true      false    dotnet-core_buildpack-cflinuxfs3-v2.2.12.zipnodejs_buildpack         6          true      false    nodejs_buildpack-cflinuxfs3-v1.6.51.zipgo_buildpack             7          true      false    go_buildpack-cflinuxfs3-v1.8.40.zippython_buildpack         8          true      false    python_buildpack-cflinuxfs3-v1.6.34.zipphp_buildpack            9          true      false    php_buildpack-cflinuxfs3-v4.3.77.zipnginx_buildpack          10         true      false    nginx_buildpack-cflinuxfs3-v1.0.13.zipr_buildpack              11         true      false    r_buildpack-cflinuxfs3-v1.0.10.zipbinary_buildpack         12         true      false    binary_buildpack-cflinuxfs3-v1.0.32.zip
```

※ 참고 URL : [https://github.com/cloudfoundry/java-buildpack](https://github.com/cloudfoundry/java-buildpack)​

### 3.4. 서비스 신청 <a id="3-4"></a>

1\) PaaS-Ta 운영자 포탈에 접속하여 로그인한다.

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff98a28ddb5443b5227c67b6cfad958badcdec820.png?generation=1615523773030027&alt=media)​

2\) 로그인 후 서비스 관리 &gt; 서비스 브로커 페이지에서 배포 파이프라인 서비스 브로커를 확인한다.

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fa9f55da7fab73f25efce834f90d5f42987cea028.png?generation=1615523773416440&alt=media)​

3\) 서비스 관리 &gt; 서비스 제어 페이지에서 배포 파이프라인 서비스 플랜 접근 가능 권한을 확인한다.

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F9f38084c3dc671c6faea5dabbd773b27ccea49a5.png?generation=1615524079236854&alt=media)​

4\) 운영관리 &gt; 카탈로그 &gt; 앱서비스 페이지를 확인하여 "파이프라인" 서비스 이름을 클릭한다.

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F90732b2fca8815a25ca77f416f8d61ac12189274.png?generation=1615523773896155&alt=media)​

* 아래의 내용을 상세 페이지에 입력한다.

> ※ 카탈로그 관리 &gt; 앱 서비스
>
> * 이름 : 파이프라인
> * 분류 : 개발 지원 도구
> * 서비스 : delivery-pipeline
> * 썸네일 : \[배포 파이프라인 서비스 썸네일\]
> * * 서비스 생성 파라미터 : owner
> * 앱 바인드 사용 : N
> * 공개 : Y
> * 대시보드 사용 : Y
> * 온디멘드 : N
> * 태그 : paasta / tag6, free / tag2
> * 요약 : 개발용으로 만들어진 파이프라인
> * 설명 : 개발용으로 만들어진 파이프라인 배포 파이프라인 Server, 배포 파이프라인 서비스 브로커로 최소사항을 구성하였다.
>
>   ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fd881fb3011cff647d6044960afccb113e3f7e54d.png?generation=1615523773628884&alt=media)​

### 3.5. 서비스 신청 - CF CLI <a id="3-5-cf-cli"></a>

* CF CLI 를 통한 파이프라인 서비스 신청 방법을 설명한다.

> $ cf create-service \[SERVICE\] \[PLAN\] \[SERVICE\_INSTANCE\] \[-c PARAMETERS\_AS\_JSON\]
>
> * \[SERVICE\] / \[PLAN\] : 서비스 명과 서비스 플랜
> * \[SERVICE\_INSTANCE\] : 서비스 인스턴스 명 \(내 서비스 목록에서 보여지는 명칭\)
> * \[-c PARAMETERS\_AS\_JSON\] : JSON 형식의 파라미터 \(파이프라인 서비스 신청 시, owner 파라미터는 필수\)

```text
### e.g. 파이프라인 서비스 신청$ cf create-service delivery-pipeline delivery-pipeline-shared pipeline-service -c '{"owner":"demo"}'  ​### e.g. 파이프라인 서비스 확인$ cf servicesGetting services in org system / space dev as admin...​name            service                  plan                        bound apps      last operation     broker               upgrade availablepipeline        delivery-pipeline        delivery-pipeline-shared                    create succeeded   delivery-pipeline
```

* 서비스 상세의 대시보드 URL 정보를 확인하여 서비스에 접근한다.

  ```text
  ### 서비스 상세 정보의 Dashboard URL을 확인한다.$ cf service pipeline... (생략) ...Dashboard:        http://115.68.47.201:8084/dashboard/2bcbe484-e235-441e-bdb6-ef88f73cb516/Service broker:   delivery-pipeline... (생략) ...
  ```

