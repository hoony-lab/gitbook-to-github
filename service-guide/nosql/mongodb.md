# Mongodb 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](mongodb.md#1) 1.1. [목적](mongodb.md#1.1) 1.2. [범위](mongodb.md#1.2) 1.3. [시스템 구성도](mongodb.md#1.3) 1.4. [참고자료](mongodb.md#1.4)​
2. 3. 4. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서\(Mongodb 서비스팩 설치 가이드\)는 전자정부표준프레임워크 기반의 PaaS-TA에서 제공되는 서비스팩인 Mongodb 서비스팩을 Bosh를 이용하여 설치 하는 방법과 PaaS-TA의 SaaS 형태로 제공하는 Application 에서 Mongodb 서비스를 사용하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 Mongodb 서비스팩을 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

본 문서의 설치된 시스템 구성도이다. Mongodb Server, Mongodb 서비스 브로커로 최소사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F7e28b57f7c3e7eceaf6150b26a1639589d4b3dcc.png?alt=media)

시스템구성도

### 1.4. 참고자료 <a id="1-4"></a>

​[**http://bosh.io/docs**](http://bosh.io/docs) [**http://docs.cloudfoundry.org/**](http://docs.cloudfoundry.org/)​

## 2. Mongodb 서비스 설치 <a id="2-mongodb"></a>

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
* MongoDB에서 사용하는 변수는 system\_domain, paasta\_admin\_username, paasta\_admin\_password, paasta\_nats\_ip, paasta\_nats\_port, paasta\_nats\_user, paasta\_nats\_password 이다.

> $ vi ~/workspace/paasta-5.5.1/deployment/common/common\_vars.yml

```text
# BOSH INFObosh_ip: "10.0.1.6"                # BOSH IPbosh_url: "https://10.0.1.6"            # BOSH URL (e.g. "https://00.000.0.0")bosh_client_admin_id: "admin"            # BOSH Client Admin IDbosh_client_admin_secret: "ert7na4jpew48"    # BOSH Client Admin Secret('echo $(bosh int ~/workspace/paasta-5.5.1/deployment/paasta-deployment/bosh/{iaas}/creds.yml --path /admin_password)' 명령어를 통해 확인 가능)bosh_director_port: 25555            # BOSH director portbosh_oauth_port: 8443                # BOSH oauth portbosh_version: 271.2                # BOSH version('bosh env' 명령어를 통해 확인 가능, on-demand service용, e.g. "271.2")​# PAAS-TA INFOsystem_domain: "61.252.53.246.xip.io"        # Domain (xip.io를 사용하는 경우 HAProxy Public IP와 동일)paasta_admin_username: "admin"            # PaaS-TA Admin Usernamepaasta_admin_password: "admin"            # PaaS-TA Admin Passwordpaasta_nats_ip: "10.0.1.121"paasta_nats_port: 4222paasta_nats_user: "nats"paasta_nats_password: "7EZB5ZkMLMqT73h2Jh3UsqO"    # PaaS-TA Nats Password (CredHub 로그인후 'credhub get -n /micro-bosh/paasta/nats_password' 명령어를 통해 확인 가능)paasta_nats_private_networks_name: "default"    # PaaS-TA Nats 의 Network 이름paasta_database_ips: "10.0.1.123"        # PaaS-TA Database IP (e.g. "10.0.1.123")paasta_database_port: 5524            # PaaS-TA Database Port (e.g. 5524(postgresql)/13307(mysql)) -- Do Not Use "3306"&"13306" in mysqlpaasta_database_type: "postgresql"                      # PaaS-TA Database Type (e.g. "postgresql" or "mysql")paasta_database_driver_class: "org.postgresql.Driver"   # PaaS-TA Database driver-class (e.g. "org.postgresql.Driver" or "com.mysql.jdbc.Driver")paasta_cc_db_id: "cloud_controller"        # CCDB ID (e.g. "cloud_controller")paasta_cc_db_password: "cc_admin"        # CCDB Password (e.g. "cc_admin")paasta_uaa_db_id: "uaa"                # UAADB ID (e.g. "uaa")paasta_uaa_db_password: "uaa_admin"        # UAADB Password (e.g. "uaa_admin")paasta_api_version: "v3"​# UAAC INFOuaa_client_admin_id: "admin"            # UAAC Admin Client Admin IDuaa_client_admin_secret: "admin-secret"        # UAAC Admin Client에 접근하기 위한 Secret 변수uaa_client_portal_secret: "clientsecret"    # UAAC Portal Client에 접근하기 위한 Secret 변수​# Monitoring INFOmetric_url: "10.0.161.101"            # Monitoring InfluxDB IPsyslog_address: "10.0.121.100"                # Logsearch의 ls-router IPsyslog_port: "2514"                              # Logsearch의 ls-router Portsyslog_transport: "relp"                        # Logsearch Protocolsaas_monitoring_url: "61.252.53.248"           # Pinpoint HAProxy WEBUI의 Public IPmonitoring_api_url: "61.252.53.241"            # Monitoring-WEB의 Public IP​### Portal INFOportal_web_user_ip: "52.78.88.252"portal_web_user_url: "http://portal-web-user.52.78.88.252.xip.io" ​### ETC INFOabacus_url: "http://abacus.61.252.53.248.xip.io"    # abacus url (e.g. "http://abacus.xxx.xxx.xxx.xxx.xip.io")
```

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/mongodb/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"                                     # stemcell osstemcell_version: "621.94"                                       # stemcell version​# NETWORKprivate_networks_name: "default"                                 # private network name​# MONGODB_REPL_SET_NAMEreplSetName1: "op1"                                              # replica set1 namereplSetName2: "op2"                                              # replica set2 namereplSetName3: "op3"                                              # replica set3 name​# MONGODB_SLAVE1mongodb_slave1_azs: [z3]                                         # mongodb slave1 azsmongodb_slave1_instances: 2                                      # mongodb slave1 instancesmongodb_slave1_vm_type: "medium"                                 # mongodb slave1 vm typemongodb_slave1_persistent_disk_type: "10GB"                      # mongodb slave1 persistent disk typemongodb_slave1_static_ips: ""        # mongodb slave1's private IPs (e.g. ["10.0.81.11","10.0.81.12"])​# MONGODB_SLAVE2mongodb_slave2_azs: [z3]                                         # mongodb slave2 azsmongodb_slave2_instances: 2                                      # mongodb slave2 instancesmongodb_slave2_vm_type: "medium"                                 # mongodb slave2 vm typemongodb_slave2_persistent_disk_type: "10GB"                      # mongodb slave2 persistent disk typemongodb_slave2_static_ips: ""        # mongodb slave2's private IPs (e.g. ["10.0.81.14","10.0.81.15"])​# MONGODB_SLAVE3mongodb_slave3_azs: [z3]                                         # mongodb slave3 azsmongodb_slave3_instances: 2                                      # mongodb slave3 instancesmongodb_slave3_vm_type: "medium"                                 # mongodb slave3 vm typemongodb_slave3_persistent_disk_type: "10GB"                      # mongodb slave3 persistent disk typemongodb_slave3_static_ips: ""        # mongodb slave3's private IPs (e.g. ["10.0.81.17","10.0.81.18"])​# MONGODB_MASTER1mongodb_master1_azs: [z3]                                                # mongodb master1 azsmongodb_master1_instances: 1                                             # mongodb master1 instancesmongodb_master1_vm_type: "medium"                                        # mongodb master1 vm typemongodb_master1_persistent_disk_type: "10GB"                             # mongodb master1 persistent disk typemongodb_master1_static_ips: ""               # mongodb master1's private IP (e.g. "10.0.81.10")mongodb_master1_replSet_hosts: ""         # 첫번째 Host는 replicaSet1 의master1 ip, 차례대로 slave1 의 ips. (e.g. ["10.0.81.10", "10.0.81.11","10.0.81.12"])​# MONGODB_MASTER2mongodb_master2_azs: [z3]                                                # mongodb master2 azsmongodb_master2_instances: 1                                             # mongodb master2 instancesmongodb_master2_vm_type: "medium"                                        # mongodb master2 vm typemongodb_master2_persistent_disk_type: "10GB"                             # mongodb master2 persistent disk typemongodb_master2_static_ips: ""               # mongodb master2's private IP (e.g. "10.0.81.13")mongodb_master2_replSet_hosts: ""         # 첫번째 Host는 replicaSet2 의master2 ip, 차례대로 slave2 의 ips. (e.g. ["10.0.81.13", "10.0.81.14","10.0.81.15"])​# MONGODB_MASTER3mongodb_master3_azs: [z3]                                                # mongodb master3 azsmongodb_master3_instances: 1                                             # mongodb master3 instancesmongodb_master3_vm_type: "medium"                                        # mongodb master3 vm typemongodb_master3_persistent_disk_type: "10GB"                             # mongodb master3 persistent disk typemongodb_master3_static_ips: ""               # mongodb master3's private IP (e.g. "10.0.81.16")mongodb_master3_replSet_hosts: ""         # 첫번째 Host는 replicaSet3 의master3 ip, 차례대로 slave3 의 ips. (e.g. ["10.0.81.16", "10.0.81.17","10.0.81.18"])​# MONGODB_CONFIGmongodb_config_azs: [z3]                                                 # mongodb config azsmongodb_config_instances: 3                                              # mongodb config instancesmongodb_config_vm_type: "medium"                                         # mongodb config vm typemongodb_config_persistent_disk_type: "10GB"                              # mongodb config persistent disk typemongodb_config_static_ips: ""                # mongodb config's private IPs (e.g. ["10.0.81.19", "10.0.81.20","10.0.81.21"])​# MONGODB_SHARDmongodb_shard_azs: [z3]                                                  # mongodb shard azsmongodb_shard_instances: 1                                               # mongodb shard instancesmongodb_shard_vm_type: "medium"                                          # mongodb shard vm typemongodb_shard_static_ips: ""                   # mongodb shard's private IP (e.g. "10.0.81.22")​# MONGODB_BROKERmongodb_broker_azs: [z3]                                                 # mongodb broker azsmongodb_broker_instances: 1                                              # mongodb broker instancesmongodb_broker_vm_type: "medium"                                         # mongodb broker vm typemongodb_broker_static_ips: ""                 # mongodb broker's private IP (e.g. "10.0.81.23")​# BROKER_REGISTRARbroker_registrar_broker_azs: [z3]                                        # broker registrar azsbroker_registrar_broker_instances: 1                                     # broker registrar instancesbroker_registrar_broker_vm_type: "medium"                                # broker registrar vm type​# BROKER_DEREGISTRARbroker_deregistrar_broker_azs: [z3]                                      # broker deregistrar azsbroker_deregistrar_broker_instances: 1                                   # broker deregistrar instancesbroker_deregistrar_broker_vm_type: "medium"                              # broker deregistrar vm type
```

'pem.yml' 은 MongoDB자체 pem을 등록해 쓰기 때문에 내용 수정하지 않는다.

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/mongodb/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d mongodb deploy --no-redact mongodb.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -l operations/pem.yml
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/mongodb  $ sh ./deploy.sh
```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
* 
```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaasta-mongodb-shard-2.0.1.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/mongodb/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""  # common_vars.yml File Path (e.g. ../../common/common_vars.yml)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"      # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d mongodb deploy --no-redact mongodb.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -l operations/pem.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/mongodb  $ sh ./deploy.sh
```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d mongodb vms

```text
Using environment '10.0.1.6' as client 'admin'​Task 8176. Done​Deployment 'mongodb'​Instance                                              Process State  AZ  IPs            VM CID                                   VM Type  Active  mongodb_broker/0e8933f1-1b67-4486-b37a-2b104da1351a   running        z5  10.30.107.114  vm-e0bb79c6-6482-497a-b071-f7df4bf2a059  minimal  true  mongodb_config/35ee66e6-9c25-44c2-85a4-e7c1d520641b   running        z5  10.30.107.111  vm-672ce5b9-4d8f-4b22-9745-43f7d9e39402  minimal  true  mongodb_config/935aed3c-e7a4-4179-b397-68d0535bc1d9   running        z5  10.30.107.112  vm-8069a84b-5a91-44ca-a5d8-cca37b5d8952  minimal  true  mongodb_config/cc798fba-7840-46ea-9211-6b5646fc766f   running        z5  10.30.107.110  vm-5a7a9d16-8de4-4adf-b504-1364716decce  minimal  true  mongodb_master1/1e8b971e-c503-4ba6-bcba-ab28dd7dd797  running        z5  10.30.107.101  vm-54b33ec2-582d-44ef-a4bf-6281acfbf81b  minimal  true  mongodb_master2/7a4460e4-a9b5-4d15-9508-adba3405f387  running        z5  10.30.107.104  vm-a388a44e-4ab9-4340-9227-b12b7bd2c410  minimal  true  mongodb_master3/88e1aa1c-fb1f-467d-a550-b6334fdfce8d  running        z5  10.30.107.107  vm-9c6aed35-69aa-4a7d-9b08-c5671e728e2a  minimal  true  mongodb_shard/1fd85812-c8d4-4ebd-98f5-c8cf637db9e5    running        z5  10.30.107.113  vm-c2628ba8-feed-4401-b1c9-be1445722d34  minimal  true  mongodb_slave1/2710c368-dbc2-4d72-a100-1fa37d73e2ec   running        z5  10.30.107.102  vm-048757cf-1c19-4c30-a3cd-2b0dd05c1554  minimal  true  mongodb_slave1/bb6275f1-4ab5-4998-ba89-ef30c36c3f67   running        z5  10.30.107.103  vm-6d0f52ef-a0b3-4c26-8e04-cb5cef30337d  minimal  true  mongodb_slave2/9671e09b-7ca1-4da2-af8a-88d20caeebfe   running        z5  10.30.107.106  vm-8a57713b-68df-4639-8ab3-3d12c01fd880  minimal  true  mongodb_slave2/fed23144-9c18-42f6-9f99-213f7dc294ee   running        z5  10.30.107.105  vm-c58e860a-8b5e-43e1-abe9-c3043cbfb16d  minimal  true  mongodb_slave3/7cebf99b-5a79-4033-a4e8-86f8d476a709   running        z5  10.30.107.108  vm-d34d8a3f-37fb-41a8-b995-6e8c7e8ff041  minimal  true  mongodb_slave3/ab6d22fb-d436-4c1c-a423-9e9d82c4266a   running        z5  10.30.107.109  vm-ca14b629-4d00-4c96-8782-1cf7a174ce1e  minimal  true  ​14 vms​Succeeded
```

## 3. Mongodb 연동 Sample Web App 설명 <a id="3-mongodb-sample-web-app"></a>

본 Sample Web App은 PaaS-TA에 배포되며 Mongodb의 서비스를 Provision과 Bind를 한 상태에서 사용이 가능하다.

### 3.1. Mongodb 서비스 브로커 등록 <a id="3-1-mongodb"></a>

Mongodb 서비스팩 배포가 완료 되었으면 Application에서 서비스 팩을 사용하기 위해서 먼저 Mongodb 서비스 브로커를 등록해 주어야 한다.

서비스 브로커 등록시 개방형 클라우드 플랫폼에서 서비스브로커를 등록할 수 있는 사용자로 로그인이 되어있어야 한다.

#### 서비스 브로커 목록을 확인한다. <a id="undefined"></a>

> `$ cf service-brokers`
>
> ​![mongodb\_image\_06](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F30c6102d474d6364d07a193ba528b3b5bf93138d.png?generation=1615854023439578&alt=media)​

#### Mongodb 서비스 브로커를 등록한다. <a id="mongodb"></a>

> `$ cf create-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 사용자비밀번호} http://{서비스팩 URL(IP)}`

**서비스팩 이름** : 서비스 팩 관리를 위해 PaaS-TA에서 보여지는 명칭이다. 서비스 Marketplace에서는 각각의 API 서비스 명이 보여지니 여기서 명칭은 서비스팩 리스트의 명칭이다. **서비스팩 사용자ID** / 비밀번호 : 서비스팩에 접근할 수 있는 사용자 ID입니다. 서비스팩도 하나의 API 서버이기 때문에 아무나 접근을 허용할 수 없어 접근이 가능한 ID/비밀번호를 입력한다. **서비스팩 URL** : 서비스팩이 제공하는 API를 사용할 수 있는 URL을 입력한다.

> `$ cf create-service-broker mongodb-shard-service-broker admin cloudfoundry http://10.30.107.114:8080`
>
> ​![mongodb\_image\_07](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F2f128d5beb07ced9669d3b0c035ede9868a13252.png?generation=1615854023660650&alt=media)​

#### 등록된 mongodb 서비스 브로커를 확인한다. <a id="mongodb-1"></a>

> `$ cf service-brokers`
>
> ​![mongodb\_image\_08](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff0981189b3dec94a83ec9e123c391ccaadf25afc.png?generation=1615854024932291&alt=media)​

#### 접근 가능한 서비스 목록을 확인한다. <a id="undefined-1"></a>

> `$ cf service-access`
>
> ​![mongodb\_image\_09](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F0b5b40bb37025adea6258d51aa352443b4d8850f.png?generation=1615854027812686&alt=media)​
>
> 서비스 브로커 생성시 디폴트로 접근을 허용하지 않는다.

#### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. \(전체 조직\) <a id="undefined-2"></a>

> `$ cf enable-service-access Mongo-DB` `$ cf service-access`
>
> ​![](https://github.com/paas-ta0812/test-5-5-0/tree/dfea4c50da10ef7a223130568dd829b87c27a69c/service-guide/images/mongodb/mongodb_image_10.png)​

### 3.2. Sample App 구조 <a id="3-2-sample-app"></a>

Sample Web App은 PaaS-TA에 App으로 배포가 된다. App을 배포하여 구동시 Bind 된 Mongodb 서비스 연결정보로 접속하여 초기 데이터를 생성하게 된다. 배포 완료 후 정상적으로 App 이 구동되면 브라우저나 curl로 해당 App에 접속 하여 Mongodb 환경정보\(서비스 연결 정보\)와 초기 적재된 데이터를 보여준다.

Sample Web App 구조는 다음과 같다.

#### PaaS-TA-Sample-Apps.zip 파일 압축을 풀고 Service 폴더안에 있는 Mongodb Sample Web App인 hello-spring-mongodb를 복사한다. <a id="paas-ta-sample-apps-zip-service-mongodb-sample-web-app-hello-spring-mongodb"></a>

> `$ ls -all`
>
> ​![mongodb\_image\_11](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F6b29344c4d8808d9ae057dc4fb3fefb3f8037f54.png?generation=1615854027323795&alt=media)​

### 3.3. PaaS-TA에서 서비스 신청 <a id="3-3-paas-ta"></a>

Sample Web App에서 Mongodb 서비스를 사용하기 위해서는 서비스 신청\(Provision\)을 해야 한다. \*참고: 서비스 신청시 개방형 클라우드 플랫폼에서 서비스를 신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.

#### 먼저 PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다. <a id="paas-ta-marketplace"></a>

> `$ cf marketplace`
>
> ​![mongodb\_image\_12](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F06072c91388a1c8a3cde941fdccbdbecc76ab4fd.png?generation=1615854029466019&alt=media)​

#### Marketplace에서 원하는 서비스가 있으면 서비스 신청\(Provision\)을 한다. <a id="marketplace-provision"></a>

> `$ cf create-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 사용자비밀번호} http://{서비스팩 URL(IP)}`

**서비스팩 이름** : 서비스 팩 관리를 위해 PaaS-TA에서 보여지는 명칭이다. 서비스 Marketplace에서는 각각의 API 서비스 명이 보여지니 여기서 명칭은 서비스팩 리스트의 명칭이다. **서비스팩 사용자ID** / 비밀번호 : 서비스팩에 접근할 수 있는 사용자 ID입니다. 서비스팩도 하나의 API 서버이기 때문에 아무나 접근을 허용할 수 없어 접근이 가능한 ID/비밀번호를 입력한다. **서비스팩 URL** : 서비스팩이 제공하는 API를 사용할 수 있는 URL을 입력한다.

> `$ cf create-service Mongo-DB default-plan mongodb-service-instance`
>
> ​![mongodb\_image\_13](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F1e866b62e0b5c83e895237b9606b95e11ea1b9ed.png?generation=1615854024563961&alt=media)​

#### 생성된 Mongodb 서비스 인스턴스를 확인한다. <a id="mongodb-2"></a>

> `$ cf services`
>
> ​![mongodb\_image\_14](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fb457f31f995b0569c904152052f39ee1702cf70b.png?generation=1615854028544321&alt=media)​

### 3.4. Sample App에 서비스 바인드 신청 및 App 확인 <a id="3-4-sample-app-app"></a>

서비스 신청이 완료되었으면 Sample Web App 에서는 생성된 서비스 인스턴스를 Bind 하여 App에서 Mongodb 서비스를 이용한다. \*참고: 서비스 Bind 신청시 개방형 클라우드 플랫폼에서 서비스 Bind신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.

#### Sample Web App 디렉토리로 이동하여 manifest 파일을 확인한다. <a id="sample-web-app-manifest"></a>

다운로드 :: [http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download](http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download)​

```text
$ wget -O sample.zip http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download$ unzip sample.zip -d sample$ cd sample/Service/hello-spring-mongodb
```

> `$ vi manifest.yml`

```text
applications:- name: hello-spring-mysql       #배포할 App 이름  memory: 1G                # 배포시 메모리 사이즈  instances: 1                    # 배포 인스턴스 수path: target/hello-spring-mysql-1.0.0-BUILD-SNAPSHOT.war      #배포하는 App 파일 PATH
```

참고: target/hello-spring-mysql-1.0.0-BUILD-SNAPSHOT.war파일이 존재 하지 않을 경우 mvn 빌드를 수행 하면 파일이 생성된다.

#### --no-start 옵션으로 App을 배포한다. <a id="no-start-app"></a>

* -no-start: App 배포시 구동은 하지 않는다.

> `$ cf push --no-start`
>
> ​![mongodb\_image\_15](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F4dcfd96e82ab06a4c852011019cb985afa93ea24.png?generation=1615178723939854&alt=media)​

#### 배포된 Sample App을 확인하고 로그를 수행한다. <a id="sample-app"></a>

> `$ cf apps`
>
> ​![mongodb\_image\_16](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F1f7fdf5585d259d6bfdda4a99fe6ff85ddbe7310.png?generation=1615178742479895&alt=media)​

> `$ cf logs hello-spring-mongodb` **// cf logs {배포된 App명}**

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fe4ddf29c1ba02ce33448b7d2abce66581aefcf9e.png?alt=media)

mongodb\_image\_17

#### Sample Web App에서 생성한 서비스 인스턴스 바인드 신청을 한다. <a id="sample-web-app"></a>

> `$ cf bind-service hello-spring-Mongodb mongodb-service-instance`
>
> ​![mongodb\_image\_42](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ffd089f83c54f30f9e3cdd624df917428a763a34c.png?generation=1615178747024677&alt=media)​

#### 바인드가 적용되기 위해서 App을 재기동한다. <a id="app"></a>

> `$ cf restart hello-spring-mongodb`
>
> ​![mongodb\_image\_18](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F270daa256c36b0457d5eb4205b3ea65180709194.png?generation=1615178743281124&alt=media)​

\(참고\) 바인드 후 App구동시 Mongodb 서비스 접속 에러로 App 구동이 안될 경우 보안 그룹을 추가한다.

#### rule.json 파일을 만들고 아래와 같이 내용을 넣는다. <a id="rule-json"></a>

> `$ vi rule.json`

```text
[  {    "protocol": "tcp",    "destination": "10.20.0.153",    "ports": "27017"  }]
```

#### 보안 그룹을 생성한다. <a id="undefined-3"></a>

> `$ cf create-security-group Mongo-DB rule.json`
>
> ​![mongodb\_image\_19](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Facd487f68c22098e9970c26c1ddfe1b03fe058bc.png?generation=1615178728263592&alt=media)​

#### 모든 App에 Mongodb 서비스를 사용할 수 있도록 생성한 보안 그룹을 적용한다. <a id="app-mongodb"></a>

> `$ cf bind-running-security-group Mongo-DB`
>
> ​![mongodb\_image\_20](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fed369c3e16d12a2ba94a9272eb34d5e270982990.png?generation=1615178737261663&alt=media)​

* App을 리부팅 한다.

```text
$ cf restart hello-spring-Mongodb
```

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fdbbd162ad567f83683ae890d84e4f0452b10a5cb.png?alt=media)

mongodb\_image\_21

#### App이 정상적으로 Mongodb 서비스를 사용하는지 확인한다. <a id="app-mongodb-1"></a>

> curl 로 확인
>
> `$ curl hello-spring-Mongodb.115.68.46.30.xip.io`
>
> ​![mongodb\_image\_22](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F49a32d9ae9d9c83dd44e841ddbe70f16b7aaa25a.png?generation=1615178721311366&alt=media)​

#### 브라우에서 확인 <a id="undefined-4"></a>

> ​![mongodb\_image\_23](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fd053d1c260e8ed6b264471e8f2f38205ee79e027.png?generation=1615178733648532&alt=media)​

## 4. Mongodb Client 툴 접속 <a id="4-mongodb-client"></a>

Application에 바인딩된 Mongodb 서비스 연결정보는 Private IP로 구성되어 있기 때문에 Mongodb Client 툴에서 직접 연결할수 없다. 따라서 SSH 터널, Proxy 터널 등을 제공하는 Mongodb Client 툴을 사용해서 연결하여야 한다. 본 가이드는 SSH 터널을 이용하여 연결 하는 방법을 제공하며 Mongodb Client 툴로써는 MongoChef 로 가이드한다. MongoChef 에서 접속하기 위해서 먼저 SSH 터널링 할수 있는 VM 인스턴스를 생성해야한다. 이 인스턴스는 SSH로 접속이 가능해야 하고 접속 후 PaaS-TA에 설치한 서비스팩에 Private IP 와 해당 포트로 접근이 가능하도록 시큐리티 그룹을 구성해야 한다. 이 부분은 OpenStack관리자 및 PaaS-TA 운영자에게 문의하여 구성한다.

### 4.1. MongoChef 설치 & 연결 <a id="4-1-mongochef-and"></a>

MongoChef 프로그램은 무료로 사용할 수 있는 소프트웨어이다.

#### MongoChef을 다운로드 하기 위해 아래 URL로 이동하여 설치파일을 다운로드 한다. <a id="mongochef-url"></a>

​[**http://3t.io/mongochef/download/platform/**](http://3t.io/mongochef/download/platform/)​

> ​![mongodb\_image\_24](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F6bc21619dcc1d224dc8b1a375083f9aee34297bb.png?generation=1615178730739112&alt=media)​

#### 다운로드한 설치파일을 실행한다. <a id="undefined-5"></a>

> ​![mongodb\_image\_25](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F3e60c4d1cad15778e75ca64ea80a4a2d01bb1d0d.png?generation=1615178734231202&alt=media)​

#### MongoChef 설치를 위한 안내사항이다. Next 버튼을 클릭한다. <a id="mongochef-next"></a>

> ​![mongodb\_image\_26](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F9b4c62d1f35d2c8c6659c36ca51d8a3c4f4b63ff.png?generation=1615178746724980&alt=media)​

#### 프로그램 라이선스에 관련된 내용이다. 동의\(I accept the terms in the License Agreement\)에 체크 후 Next 버튼을 클릭한다. <a id="i-accept-the-terms-in-the-license-agreement-next"></a>

> ​![mongodb\_image\_27](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fe496b6323cc46da5ec726b1490da42f0f2686293.png?generation=1615178733891906&alt=media)​

#### MongoChef 을 설치할 경로를 설정 후 Next 버튼을 클릭한다. 별도의 경로 설정이 필요 없을 경우 default로 C드라이브 Program Files 폴더에 설치가 된다. <a id="mongochef-next-default-c-program-files"></a>

> ​![mongodb\_image\_28](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F3e1610d11875c0a877bab6f3c06f1a79d6e9c970.png?generation=1615178746917281&alt=media)​

#### Install 버튼을 클릭하여 설치를 진행한다. <a id="install"></a>

> ​![mongodb\_image\_29](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F276a67a0ed25bce7ab0d6fd405979aa491df3b36.png?generation=1615178744304476&alt=media)​

#### Finish 버튼 클릭으로 설치를 완료한다. <a id="finish"></a>

> ​![mongodb\_image\_30](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fb9e8e29a21435a70d1d4552a2a8068b5d44f1b29.png?generation=1615178746307537&alt=media)​

#### MongoChef를 실행했을 때 처음 뜨는 화면이다. 이 화면에서 Server에 접속하기 위한 profile을 설정/저장하여 접속할 수 있다. Connect버튼을 클릭한다. <a id="mongochef-server-profile-connect"></a>

> ​![mongodb\_image\_31](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F08858bf474cfbeabd4be0b6ab3daf4e3a8a03cb2.png?generation=1615178745397671&alt=media)​

#### 새로운 접속 정보를 작성하기 위해New Connection 버튼을 클릭한다. <a id="new-connection"></a>

> ​![mongodb\_image\_32](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F737633f82cb286538f2384c5fa3bfac98e198bf6.png?generation=1615178724544237&alt=media)​

#### Server에 접속하기 위한 Connection 정보를 입력한다. <a id="server-connection"></a>

> ​![mongodb\_image\_33](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fdc4557f863644b6e18e620931adde62c2585cf83.png?generation=1615178726797210&alt=media)​

#### 서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env 명령어로 이용하여 확인한다. <a id="application-cf-env"></a>

> `$ cf env hello-spring-mongodb`

> ​![mongodb\_image\_34](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F6fcdaefa00ba7d8d7759fcd60a33e43d227e1af5.png?generation=1615178738336140&alt=media)​

#### Authentication탭으로 이동하여 mongodb 의 인증정보를 입력한다. <a id="authentication-mongodb"></a>

> ​![mongodb\_image\_35](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fc1ffdd2c3fd981f23c180bf3f086e8555d77490f.png?generation=1615178736490772&alt=media)​

#### SSH 터널 탭을 클릭하고 PaaS-TA 운영 관리자에게 제공받은 SSH 터널링 가능한 서버 정보를 입력한다. <a id="ssh-paas-ta-ssh"></a>

> ​![mongodb\_image\_36](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff3ce02a5ae9d378ba33bf2fc310fbb68742fa22c.png?generation=1615178741315110&alt=media)​

#### 모든 정보를 입력했으면 Test Connection 버튼을 눌러 접속 테스트를 한다. <a id="test-connection"></a>

> ​![mongodb\_image\_37](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F6dd46b4e304832748c126a76edc9d351c290f225.png?generation=1615178727356801&alt=media)​

#### 모두 OK 결과가 나오면 정상적으로 접속이 된다는 것이다. OK 버튼을 누른다. <a id="ok-ok"></a>

#### Save 버튼을 눌러 작성한 접속정보를 저장한다. <a id="save"></a>

> ​![mongodb\_image\_38](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fe26bab2175b89b418461255ba4f2f167de6e089b.png?generation=1615178722273024&alt=media)​

#### 방금 저장한 접속정보를 선택하고 Connect 버튼을 클릭하여 접속한다. <a id="connect"></a>

> ​![mongodb\_image\_39](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F68c4ccf789e11db4024fe14f81a75bb074a108e8.png?generation=1615178725253128&alt=media)​

#### 접속이 완료되면 좌측에 스키마 정보가 나타난다. 컬럼을 더블클릭 해보면 우측에 적재되어있는 데이터가 출력된다. <a id="undefined-6"></a>

> ​![mongodb\_image\_40](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F5b1f7a8fce39b0adad9226ff286e3d3e9cf31da5.png?generation=1615178729328892&alt=media)​

#### 우측 화면에 쿼리 항목에 Query문을 작성한 후 실행 버튼\(삼각형\)을 클릭한다. Query문에 이상이 없다면 정상적으로 결과를 얻을 수 있을 것이다. <a id="query-query"></a>

> ​![mongodb\_image\_41](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff8edba53b55177c659f008ece1b22b90a667dad3.png?generation=1615178731891683&alt=media)​

