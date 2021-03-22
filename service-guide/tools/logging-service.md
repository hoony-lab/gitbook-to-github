# Logging Service 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](logging-service.md#1) 1.1. [목적](logging-service.md#1.1) 1.2. [범위](logging-service.md#1.2) 1.3. [시스템 구성](logging-service.md#1.3) 1.4. [참고자료](logging-service.md#1.4)​
2. 3. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서는 Logging 서비스 Release를 Bosh2.0을 이용하여 설치 하는 방법을 기술하였다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 Logging 서비스 Release를 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성 <a id="1-3"></a>

본 장에서는 Logging 서비스의 시스템 구성에 대해 기술하였다. Logging 서비스 시스템은 Router, Collector, Queue, Parser, Elasticsearch, Visualization의 최소사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F54c2c010c319e7f0bd6db6b7a9c12c126f68e084.png?alt=media)

시스템 구성도

### 1.4. 참고자료 <a id="1-4"></a>

> ​[http://bosh.io/docs](http://bosh.io/docs) [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)​

## 2. Logging 서비스 설치 <a id="2-logging"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0 이상, PaaS-TA 포털이 설치되어 있어야 한다.

※ "firehose-to-syslog" uaac client 확인

uaac client에 "firehose-to-syslog"가 등록되어 있는지 확인 하여, 등록되어 있는 경우에는 "authorities"를 확인하여 "cloud\_controller.admin" 권한을 부여한다.

```text
# endpoint 설정$ uaac target https://uaa. --skip-ssl-validation​# target 확인$ uaac targetTarget: https://uaa.Context: uaa_admin, from client uaa_admin​# uaac 로그인$ uaac token client get  -s ​# "firehose-to-syslog" uaac client 확인$ uaac client get firehose-to-syslogscope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.writeclient_id: firehose-to-syslogresource_ids: noneauthorized_grant_types: client_credentialsautoapprove:authorities: uaa.none doppler.firehose                   >>>>>>>>  cloud_controller.admin 권한 여부 확인lastmodified: 1552530293656​# "firehose-to-syslog" uaac client 변경$ uaac client update firehose-to-syslog --authorities "doppler.firehose, uaa.none, cloud_controller.admin"​# "firehose-to-syslog" uaac client 확인$ uaac client get firehose-to-syslogscope: cloud_controller.admin_read_only cloud_controller.global_auditor openid routing.router_groups.write network.write scim.read cloud_controller.admin uaa.user cloud_controller.read    password.write routing.router_groups.read cloud_controller.write network.admin doppler.firehose scim.writeclient_id: firehose-to-syslogresource_ids: noneauthorized_grant_types: client_credentialsautoapprove:authorities: uaa.none doppler.firehose cloud_controller.adminlastmodified: 1552530293656
```

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
* Logging 서비스에서 사용하는 변수는 system\_domain, uaa\_client\_admin\_id, uaa\_client\_admin\_secret 이다.

> $ vi ~/workspace/paasta-5.5.1/deployment/common/common\_vars.yml

```text
# BOSH INFObosh_ip: "10.0.1.6"                # BOSH IPbosh_url: "https://10.0.1.6"            # BOSH URL (e.g. "https://00.000.0.0")bosh_client_admin_id: "admin"            # BOSH Client Admin IDbosh_client_admin_secret: "ert7na4jpew48"    # BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-5.5.1/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' 명령어를 통해 확인 가능)bosh_director_port: 25555            # BOSH director portbosh_oauth_port: 8443                # BOSH oauth portbosh_version: 271.2                # BOSH version('bosh env' 명령어를 통해 확인 가능, on-demand service용, e.g. "271.2")​# PAAS-TA INFOsystem_domain: "61.252.53.246.xip.io"        # Domain (xip.io를 사용하는 경우 HAProxy Public IP와 동일)paasta_admin_username: "admin"            # PaaS-TA Admin Usernamepaasta_admin_password: "admin"            # PaaS-TA Admin Passwordpaasta_nats_ip: "10.0.1.121"paasta_nats_port: 4222paasta_nats_user: "nats"paasta_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"    # PaaS-TA Nats Password (CredHub 로그인후 'credhub get -n /micro-bosh/paasta/nats_password' 명령어를 통해 확인 가능)paasta_nats_private_networks_name: "default"    # PaaS-TA Nats 의 Network 이름paasta_database_ips: "10.0.1.123"        # PaaS-TA Database IP (e.g. "10.0.1.123")paasta_database_port: 5524            # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysqlpaasta_database_type: "postgresql"                      # PaaS-TA Database Type (e.g. "postgresql" or "mysql")paasta_database_driver_class: "org.postgresql.Driver"   # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")paasta_cc_db_id: "cloud_controller"        # CCDB ID (e.g. "cloud_controller")paasta_cc_db_password: "cc_admin"        # CCDB Password (e.g. "cc_admin")paasta_uaa_db_id: "uaa"                # UAADB ID (e.g. "uaa")paasta_uaa_db_password: "uaa_admin"        # UAADB Password (e.g. "uaa_admin")paasta_api_version: "v3"​# UAAC INFOuaa_client_admin_id: "admin"            # UAAC Admin Client Admin IDuaa_client_admin_secret: "admin-secret"        # UAAC Admin Client에 접근하기 위한 Secret 변수uaa_client_portal_secret: "clientsecret"    # UAAC Portal Client에 접근하기 위한 Secret 변수​# Monitoring INFOmetric_url: "10.0.161.101"            # Monitoring InfluxDB IPsyslog_address: "10.0.121.100"                # Logsearch의 ls-router IPsyslog_port: "2514"                              # Logsearch의 ls-router Portsyslog_transport: "relp"                        # Logsearch Protocolsaas_monitoring_url: "61.252.53.248"           # Pinpoint HAProxy WEBUI의 Public IPmonitoring_api_url: "61.252.53.241"            # Monitoring-WEB의 Public IP​### Portal INFOportal_web_user_ip: "52.78.88.252"portal_web_user_url: "http://portal-web-user.52.78.88.252.xip.io" ​### ETC INFOabacus_url: "http://abacus.61.252.53.248.xip.io"    # abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.xip.io")
```

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/logging-service/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"                                     # stemcell osstemcell_version: "621.94"                                       # stemcell version​# VM_TYPEvm_type_minimal: "minimal"                                       # vm type minimalvm_type_default: "small"                                         # vm type smallvm_type_medium: "medium"                                         # vm type medium​# NETWORKprivate_networks_name: "default"                                 # private network name public_networks_name: "vip"                                      # public network nameprivate_nat_networks_name: "default"                             # AWS의 경우, NATS Network Name​# ELASTICSEARCH_MASTERes_master_azs: [z3]                                              # elasticsearch master : azses_master_instances: 1                                           # elasticsearch master : instances (1) es_master_persistent_disk_type: "10GB"                           # elasticsearch master : persistent disk typees_master_private_ips: ""                 # elasticsearch master : private ips (e.g. ["10.0.81.11"])es_master_private_url: ""                 # elasticsearch master : private url (e.g. "10.0.81.11")​# QUEUEqueue_azs: [z3]                                                  # queue : azsqueue_instances: 1                                               # queue : instances (1)queue_persistent_disk_type: "10GB"                               # queue : persistent disk typequeue_private_ips: ""                         # queue : private ips (e.g. ["10.0.81.12"])queue_private_url: ""                         # queue : private url (e.g. "10.0.81.12")​# MAINTENANCEmaintenance_azs: [z3]                                            # maintenance : azsmaintenance_instances: 1                                         # maintenance : instances (1)maintenance_private_ips: ""             # maintenance : private ips (e.g. ["10.0.81.13"])​# ELASTICSEARCH_DATAes_data_azs: [z3]                                                # elasticsearch data : azs es_data_instances: 2                                             # elasticsearch data : instances (N)es_data_persistent_disk_type: "20GB"                             # elasticsearch data : persistent disk typees_data_private_ips: ""                     # elasticsearch data : private ips (e.g. ["10.0.81.14", "10.0.81.15"])​# VISUALIZATIONvisualization_azs: [z3]                                          # visualization : azsvisualization_instances: 1                                       # visualization : instances (1) visualization_private_ips: ""         # visualization : private ips (e.g. ["10.0.81.16"])visualization_version:  "5.3.0"                                  # visualization : version (5.3.0)​# COLLECTORcollector_azs: [z3]                                              # collector : azscollector_instances: 1                                           # collector : instances (1)collector_private_ips: ""                 # collector : private ips (e.g. ["10.0.81.17"])​# PARSERparser_azs: [z3]                                                 # parser : azsparser_instances: 2                                              # parser : instances (N)parser_private_ips: ""                       # parser : private ips (e.g. ["10.0.81.18", "10.0.81.19"])parser_es_index: "%{[@metadata][index]}-%{+YYYY.MM.dd.HH}"       # parser : elasticsearch indexparser_es_index_type: '%{[@metadata][type]}'                     # parser : elasticsearch index type​# ROUTERrouter_azs: [z7]                                                 # router : azs router_instances: 1                                              # router : instances (1)router_private_ips: ""                       # router : private ips (e.g. ["10.0.0.101"])router_public_ips: ""                         # router : public ips (e.g. "13.209.212.226")router_private_url: ""                       # router : private url (e.g. "10.0.0.101")​# UAACuaa_client_laas_id: "laasclient"                                 # logging service uaa client iduaa_client_laas_secret: "clientsecret"                           # logging service uaa client secret​# LOGGING SERVICEes_config_index_prefix: "laas-"                                  # logging service elasticsearch index prefix ("laas-")retention_period: 7                                              # logging service retention periodlaas_logo: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAALQAAABGCAYAAABll74gAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAA3FpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTQyIDc5LjE2MDkyNCwgMjAxNy8wNy8xMy0wMTowNjozOSAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6MkRBNUQ0REMyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6MkRBNUQ0REIyQ0EwMTFFOEI2QkZBQUY2QUYwM0UzRjkiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIChXaW5kb3dzKSI+IDx4bXBNTTpEZXJpdmVkRnJvbSBzdFJlZjppbnN0YW5jZUlEPSJ4bXAuaWlkOjQ5YzFjOTUyLWE2MDEtZTI0NC1iMzIzLTMzYTIyNzI1NDcxYiIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDpkYzhkNmI4YS1kZWNkLThkNGItOThiNC0zMjRjZjU1OTE0NmYiLz4gPC9y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"
```

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/logging-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"              # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d logging-service deploy --no-redact logging-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/logging-service$ sh ./deploy.sh
```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
* 
```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaasta-logging-service-release.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/logging-service/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"                # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d logging-service deploy --no-redact logging-service.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/logging-service$ sh ./deploy.sh
```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d logging-service vms

```text
Using environment '10.30.40.111' as client 'admin'​Task 68432. Done​Deployment 'logging-service'​Instance                                                   Process State  AZ  IPs            VM CID                                   VM Type  Active  collector/d2a1aed9-d10f-42df-91ec-e21f1baecfb8             running        z5  10.30.107.131  vm-e73085ec-e336-4c54-a842-37989dc4fe1d  default  true  elasticsearch_data/d779c528-8f75-4b4c-b2d9-ac367c1e5ece    running        z5  10.30.107.133  vm-5b1fed2f-774f-47cf-9a14-edc015e790f1  medium   true  elasticsearch_data/fa38698e-913c-4296-aac8-c0b56c84a71e    running        z5  10.30.107.134  vm-36a47dab-8d09-4daa-bb8c-0394f4d83fd7  medium   true  elasticsearch_master/4698c36b-413d-4370-b671-44ee075a0cf0  running        z5  10.30.107.135  vm-46152b8f-d660-413c-9396-8b4068a4a454  default  true  maintenance/dba09e1e-06c0-42bf-a30d-d97a62c536bc           running        z5  10.30.107.136  vm-780e1595-9aa9-445c-b056-27ff4e844017  minimal  true  parser/3dfdc7bc-8dde-4ed1-95d0-eb638d4900fa                running        z5  10.30.107.138  vm-44210be7-0dab-46db-8cb6-d71a2c29d3c8  default  true  parser/7ef8ffd6-7d8b-4ae0-bd8c-17f5e7092ca2                running        z5  10.30.107.137  vm-1eb78459-3050-4ab2-8f49-78f0ddb795b0  default  true  queue/cc986003-b6c1-4570-b2d7-32ecfd40eedf                 running        z5  10.30.107.139  vm-f11ec996-5c1e-46a0-972a-8b1415267df0  default  true  router/c64e9519-713c-4f24-9b04-4bbf2d0ac457                running        z5  10.30.107.140  vm-32ebc53c-6bef-48d7-854e-4b09a4dd9d01  minimal  true                                                                                115.68.47.181                                                      visualization/d1ac0c78-aa4c-465d-9193-64f2e2de269a         running        z5  10.30.107.143  vm-75fdb6a6-e77f-4adb-8336-ec77254c82fa  default  true
```

## 3. Logging 서비스 관리 <a id="3-logging"></a>

서비스 설치가 완료 되면, PaaS-TA 포탈에서 서비스를 사용하기 위해 Logging 서비스 UAA Client 등록 및 Logging 서비스 활성화 코드 등록을 해 주어야 한다.

### 3.1. UAA Client 등록 <a id="3-1-uaa-client"></a>

* uaac server의 endpoint를 설정한다.

```text
# endpoint 설정$ uaac target https://uaa. --skip-ssl-validation​# target 확인$ uaac targetTarget: https://uaa.Context: uaa_admin, from client uaa_admin
```

* uaac 로그인을 한다.

```text
$ uaac token client get  -s Successfully fetched token via client credentials grant.Target: https://uaa.Context: admin, from client admin
```

* Logging 서비스 계정을 생성 한다. $ uaac client add -s --redirect\_uri --scope &lt;퍼미션 범위&gt; --authorized\_grant\_types &lt;권한 타입&gt; --authorities=&lt;권한 퍼미션&gt; --autoapprove=&lt;자동승인권한&gt;
  *  : uaac 클라이언트 id
  *  : uaac 클라이언트 secret
  *  : 성공적으로 리다이렉션 할 Logging 서비스 접근 URI \(http://\)
  * &lt;퍼미션 범위&gt; : 클라이언트가 사용자를 대신하여 얻을 수있는 허용 범위 목록
  * &lt;권한 타입&gt; : 서비스가 제공하는 API를 사용할 수 있는 권한 목록
  * &lt;권한 퍼미션&gt; : 클라이언트에 부여 된 권한 목록
  * &lt;자동승인권한&gt; : 사용자 승인이 필요하지 않은 권한 목록

```text
# e.g. Logging 서비스 계정 생성$ uaac client add laasclient -s clientsecret --redirect_uri " http://115.68.47.181" \  --scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \  --authorized_grant_types "authorization_code , client_credentials , refresh_token" \  --authorities="uaa.resource" \--autoapprove="openid , cloud_controller_service_permissions.read"​# e.g. Logging 서비스 계정 생성 확인$ uaac clientslaasclient    scope: cloud_controller.read cloud_controller.write cloud_controller_service_permissions.read openid        cloud_controller.admin    resource_ids: none    authorized_grant_types: refresh_token client_credentials authorization_code    redirect_uri: http://115.68.47.181    autoapprove: cloud_controller_service_permissions.read openid    authorities: uaa.resource    name: laasclient    lastmodified: 1542894096080
```

## 3.2. Logging 서비스 활성화 코드 등록 <a id="3-2-logging"></a>

* PaaS-TA 운영자 포탈에 접속한다.

  > ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff885ac90478a16234b10130808c6267fcff87549.png?generation=1615516013833748&alt=media)​

* 운영관리의 코드관리 메뉴로 이동하여 다음과 같이 코드를 등록한다.

> ※ Group Table 코드 ID : LAAS 코드 이름 : Logging Service ![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fe5ee6853524dba444b44a92a3bf80add6433a473.png?generation=1615185747213894&alt=media)​
>
> ※ Detail Table Key : laas\_base\_url Value : http:///app/laas 요약 : Logging Service Base URL 사용 : Y ![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fd2096a80e3bb037543289118243e710fdf6581e4.png?generation=1615185747209886&alt=media)​

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F55ebc6a533815a02d0142f4d3bf955a5acdaaa30.png?alt=media)

