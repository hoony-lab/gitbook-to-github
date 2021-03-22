# PAAS-TA\_MARKETPLACE\_ENVIRONMENT\_INSTALL\_GUIDE\_V1.0

### Table of Contents

1. [문서 개요](paas-ta_marketplace_environment_install_guide_v1.0.md#1) 1.1. [목적](paas-ta_marketplace_environment_install_guide_v1.0.md#1.1) 1.2. [범위](paas-ta_marketplace_environment_install_guide_v1.0.md#1.2) 1.3. [시스템 구성](paas-ta_marketplace_environment_install_guide_v1.0.md#1.3) 1.4. [참고자료](paas-ta_marketplace_environment_install_guide_v1.0.md#1.4)
2. [Marketplace Environment 설치](paas-ta_marketplace_environment_install_guide_v1.0.md#2) 2.1. [Prerequisite](paas-ta_marketplace_environment_install_guide_v1.0.md#2.1) 2.2. [Stemcell 확인](paas-ta_marketplace_environment_install_guide_v1.0.md#2.2) 2.3. [Deployment 다운로드](paas-ta_marketplace_environment_install_guide_v1.0.md#2.3) 2.4. [Deployment 파일 수정](paas-ta_marketplace_environment_install_guide_v1.0.md#2.4) 2.5. [서비스 설치](paas-ta_marketplace_environment_install_guide_v1.0.md#2.5) 2.6. [서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식](paas-ta_marketplace_environment_install_guide_v1.0.md#2.6) 2.7. [서비스 설치 확인](paas-ta_marketplace_environment_install_guide_v1.0.md#2.7)

## Deprecated

**※ 본 문서는 PaaS-TA 5.1 이하의 버전까지 지원한다.**

### 1. 문서 개요

#### 1.1. 목적

본 문서는 Marketplace 서비스에 필요한 제반 환경을 구축하기 위해 Marketplace Environment Release를 Bosh2.0을 이용하여 설치 하는 방법을 기술하였다.

#### 1.2. 범위

설치 범위는 Marketplace 서비스에 필요한 기본 환경 구성을 위한 설치를 기준으로 작성하였다.

#### 1.3. 시스템 구성

본 장에서는 Marketplace Environment 구성에 대해 기술하였다. Marketplace 서비스에 필요한 환경은 binary\_storage, mariadb의 최소사항을 구성하였다.

| VM명 | 인스턴스 수 | vCPU수 | 메모리\(GB\) | 디스크\(GB\) |
| :--- | :---: | :---: | :---: | :--- |
| binary\_storage | 1 | 1vCPU | 1GB | 2GB |
| mariadb | 1 | 1vCPU | 2GB | 50GB |

#### 1.4. 참고자료

> [http://bosh.io/docs](http://bosh.io/docs)  
> [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)

### 2. Marketplace Environment 설치

#### 2.1. Prerequisite

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0, PaaS-TA 포털이 설치되어 있어야 한다.

#### 2.2. Stemcell 확인

Stemcell 목록을 확인하여 서비스 설치에 필요한 Stemcell이 업로드 되어 있는 것을 확인한다. \(PaaS-TA 5.0 과 동일 stemcell 사용\)

> $ bosh -e micro-bosh stemcells

```text
Using environment '10.0.1.6' as client 'admin'

Name                                     Version  OS             CPI  CID  
bosh-aws-xen-hvm-ubuntu-xenial-go_agent  315.64*  ubuntu-xenial  -    ami-0297ff649e8eea21b  

(*) Currently deployed

1 stemcells

Succeeded
```

#### 2.3. Deployment 다운로드

서비스 설치에 필요한 Deployment를 Git Repository에서 받아 서비스 설치 작업 경로로 위치시킨다.

* Service Deployment Git Repository URL : [https://github.com/PaaS-TA/service-deployment/tree/v5.0.3](https://github.com/PaaS-TA/service-deployment/tree/v5.0.3)

```text
# Deployment 다운로드 파일 위치 경로 생성 및 설치 경로 이동
$ mkdir -p ~/workspace/paasta-5.0/deployment
$ cd ~/workspace/paasta-5.0/deployment

# Deployment 파일 다운로드
$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.0.3
```

#### 2.4. Deployment 파일 수정

BOSH Deployment manifest는 Components 요소 및 배포의 속성을 정의한 YAML 파일이다. Deployment 파일에서 사용하는 network, vm\_type, disk\_type 등은 Cloud config를 활용하고, 활용 방법은 BOSH 2.0 가이드를 참고한다.

* Cloud config 설정 내용을 확인한다.   

> $ bosh -e micro-bosh cloud-config

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

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.0/deployment/service-deployment/marketplace/vars.yml

```text
# STEMCELL
stemcell_os: "ubuntu-xenial"                                     # stemcell os
stemcell_version: "315.64"                                       # stemcell version


# NETWORK
private_networks_name: "default"                                 # private network name
public_networks_name: "vip"                                      # public network name


# OBJECT_STORAGE
object_storage_azs: [z7]                                         # object storage : azs
object_storage_instances: 1                                      # object storage : instances (1)
object_storage_persistent_disk_type: "10GB"                      # object storage : persistent disk type
object_storage_vm_type: "medium"                                 # object storage : vm type
object_storage_public_static_ips: "<BINARY_PUBLIC_STATIC_IPS>"   # object storage : public IP
object_storage_auth_port: "<OBJECT_STORAGE_AUTH_PORT>"           # object storage : keystone port (e.g. 15001) -- Do Not Use "5000"
object_storage_username: "paasta-marketplace"                    # object storage : username (e.g. "paasta-marketplace")
object_storage_password: "paasta"                                # object storage : password (e.g. "paasta")
object_storage_tenantname: "paasta-marketplace"                  # object storage : tenantname (e.g. "paasta-marketplace")
object_storage_email: "email@email.com"                          # object storage : email (e.g. "paasta@paasta.com")
object_storage_container: "marketplace-container"                # object storage : swift container (e.g. marketplace-container)


# MARIA DB
mariadb_azs: [z3]                                                # mariadb : azs
mariadb_instances: 1                                             # mariadb : instances (1)
maraidb_persistent_disk_type: "5GB"                              # mariadb : persistent disk type
mariadb_vm_type: "small"                                         # mariadb : vm type
mariadb_port: "<MARIADB_PORT>"                                   # mariadb : port (e.g. 13306) -- Do Not Use "3306"
mariadb_admin_password: "<MARIADB_ADMIN_PASSWORD>"               # mariadb : admin password (e.g. admin@1234)
```

#### 2.5. 서비스 설치

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정한다. 

> $ vi ~/workspace/paasta-5.0/deployment/service-deployment/marketplace/deploy.sh

```text
#!/bin/bash

# VARIABLES
BOSH_NAME="<BOSH_NAME>"                          # bosh name (e.g. micro-bosh)
IAAS="<IAAS_NAME>"                               # IaaS (e.g. aws/azure/gcp/openstack/vsphere)
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"       # common_vars.yml File Path (e.g. /home/ubuntu/paasta-5.0/common/common_vars.yml)

bosh -e ${BOSH_NAME} -n -d marketplace deploy --no-redact marketplace.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml
```

* 서비스를 설치한다.  

  ```text
  $ cd ~/workspace/paasta-5.0/deployment/service-deployment/marketplace  
  $ sh ./deploy.sh
  ```

#### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
  * 설치 릴리즈 파일 다운로드 : [paasta-marketplace-env-release-1.0.1.tgz](http://45.248.73.44/index.php/s/mDmMGB3qkN4MQjB/download)

```text
# 릴리즈 다운로드 파일 위치 경로 생성
$ mkdir -p ~/workspace/paasta-5.0/release/service

# 릴리즈 파일 다운로드 및 파일 경로 확인
$ ls ~/workspace/paasta-5.0/release/service/marketplace
paasta-marketplace-env-release-1.0.1.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.  

     \(추가\) -o operations/use-compiled-releases.yml  

     \(추가\) -v inception\_os\_user\_name=""  

> $ vi ~/workspace/paasta-5.0/deployment/service-deployment/marketplace/deploy.sh

```text
#!/bin/bash

# VARIABLES
BOSH_NAME="<BOSH_NAME>"                          # bosh name (e.g. micro-bosh)
IAAS="<IAAS_NAME>"                               # IaaS (e.g. aws/azure/gcp/openstack/vsphere)
COMMON_VARS_PATH="<COMMON_VARS_FILE_PATH>"       # common_vars.yml File Path (e.g. /home/ubuntu/paasta-5.0/common/common_vars.yml)

bosh -e ${BOSH_NAME} -n -d marketplace deploy --no-redact marketplace.yml \
    -o operations/use-compiled-releases.yml \
    -l ${COMMON_VARS_PATH} \
    -l vars.yml \
    -v inception_os_user_name="ubuntu"
```

* 서비스를 설치한다.  

  ```text
  $ cd ~/workspace/paasta-5.0/deployment/service-deployment/marketplace  
  $ sh ./deploy.sh
  ```

#### 2.7. 서비스 설치 확인

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d marketplace vms
>
> \`\`\` Deployment 'marketplace'

Instance Process State AZ IPs VM CID VM Type Active binary\_storage/66e5bf20-da8d-42b4-a325-fba5f6e326e8 running z7 10.174.1.56 vm-a81d9fe1-e9e8-4729-9786-bbb5f1518234 medium true xxx.xxx.xxx mariadb/01ce2b6f-1038-468d-92f8-f68f72f7ea77 running z2 10.174.1.57 vm-ce5deeed-ba4e-49d1-b6ab-1f07c779e776 small true

\`\`\`

