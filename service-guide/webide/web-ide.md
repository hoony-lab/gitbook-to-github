# WEB-IDE 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](web-ide.md#1) 1.1. [목적](web-ide.md#1.1) 1.2. [범위](web-ide.md#1.2) 1.3. [시스템 구성도](web-ide.md#1.3) 1.4. [참고자료](web-ide.md#1.4)​
2. 3. 4. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서는 PaaS-TA에서 사용할 수 있는 WEB-IDE의 설치를 Bosh를 이용하여 설치 하는 방법과 PaaS-TA 포털에서 WEB-IDE 서비스를 사용하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 WEB-IDE 사용을 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

본 장에서는 WEB-IDE의 시스템 구성에 대해 기술하였다. Browser\(PaaS-TA Portal\), WEB IDE Server, Workspace, Desktop IDE로 최소사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F835f0a756756abe90347556c5235d00d09be1cac.png?alt=media)

### 1.4. 참고자료 <a id="1-4"></a>

> ​[**http://bosh.io/docs**](http://bosh.io/docs) [**http://docs.cloudfoundry.org/**](http://docs.cloudfoundry.org/) [**https://www.eclipse.org/che/technology/**](https://www.eclipse.org/che/technology/)

## 2. WEB IDE 설치 <a id="2-web-ide"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다. 서비스 설치를 위해서는 BOSH 2.0과 PaaS-TA 5.0, PaaS-TA 포털이 설치되어 있어야 한다.

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
# Deployment 다운로드 파일 위치 경로 생성 및 설치 경로 이동$ mkdir -p ~/workspace/paasta-5.5.1/deployment$ cd ~/workspace/paasta-5.5.1/deployment​# Deployment 파일 다운로드$ git clone https://github.com/PaaS-TA/service-deployment.git -b v5.0.6
```

### 2.4. Deployment 파일 수정 <a id="2-4-deployment"></a>

BOSH Deployment manifest는 Components 요소 및 배포의 속성을 정의한 YAML 파일이다. Deployment 파일에서 사용하는 network, vm\_type, disk\_type 등은 Cloud config를 활용하고, 활용 방법은 BOSH 2.0 가이드를 참고한다.

* Cloud config 설정 내용을 확인한다.

> $ bosh -e micro-bosh cloud-config

```text
Using environment '10.0.1.6' as client 'admin'​azs:- cloud_properties:    availability_zone: ap-northeast-2a  name: z1- cloud_properties:    availability_zone: ap-northeast-2a  name: z2​... ((생략)) ...​disk_types:- disk_size: 1024  name: default- disk_size: 1024  name: 1GB​... ((생략)) ...​networks:- name: default  subnets:  - az: z1    cloud_properties:      security_groups: paasta-security-group      subnet: subnet-00000000000000000    dns:    - 8.8.8.8    gateway: 10.0.1.1    range: 10.0.1.0/24    reserved:    - 10.0.1.2 - 10.0.1.9    static:    - 10.0.1.10 - 10.0.1.120​... ((생략)) ...​vm_types:- cloud_properties:    ephemeral_disk:      size: 3000      type: gp2    instance_type: t2.small  name: minimal- cloud_properties:    ephemeral_disk:      size: 10000      type: gp2    instance_type: t2.small  name: small​... ((생략)) ...​Succeeded
```

* Deployment YAML에서 사용하는 변수 파일을 서버 환경에 맞게 수정한다.

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/web-ide/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"                                         # stemcell osstemcell_version: "621.94"                                           # stemcell version​# NETWORKprivate_networks_name: "default"                                     # private network namepublic_networks_name: "vip"                                          # public network name​# ECLIPSE-CHEeclipse_che_azs: [z7]                                                # eclipse-che : azseclipse_che_instances: 1                                             # eclipse-che : instances (1)eclipse_che_vm_type: "large"                                         # eclipse-che : vm typeeclipse_che_public_ips: ""                   # eclipse-che : public ips (e.g. ["00.00.00.00" , "11.11.11.11"])​# MARIA_DBmariadb_azs: [z3]                                                    # mariadb : azsmariadb_instances: 1                                                 # mariadb : instances (1) mariadb_vm_type: "small"                                             # mariadb : vm typemariadb_persistent_disk_type: "10GB"                                 # mariadb : persistent disk typemariadb_port: ""                                       # mariadb : database port (e.g. 31306) -- Do Not Use "3306"mariadb_admin_password: ""                   # mariadb : database admin password (e.g. "[email protected]")​# SERVICE-BROKERbroker_azs: [z3]                                                     # service-broker : azsbroker_instances: 1                                                  # service-broker : instances (1)broker_vm_type: "medium"                                             # service-broker : vm typebroker_port: ""                                         # service-broker : broker port (e.g. "8080")broker_services_id: ""                           # service-broker : service guid (e.g. "af86588c-6212-11e7-907b-b6006ad3webide0")broker_services_plans_id: ""               # service-broker : service plan id (e.g. "a5930564-6212-11e7-907b-b6006ad3webide1")
```

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/web-ide/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d web-ide deploy --no-redact web-ide.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml
```

* 서비스를 설치한다.

  ```text
  $ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/web-ide$ sh ./deploy.sh
  ```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
  * 

```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaas-ta-webide-release-1.1.0.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/web-ide/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)CURRENT_IAAS="${CURRENT_IAAS}"            # IaaS Information (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 aws/azure/gcp/openstack/vsphere 입력)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d web-ide deploy --no-redact web-ide.yml \    -o operations/${CURRENT_IAAS}-network.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

  ```text
  $ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/web-ide$ sh ./deploy.sh
  ```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d web-id vms

```text
Using environment '10.30.40.111' as user 'admin' (openid, bosh.admin)​Task 7872. Done​Deployment 'web-id'​Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Activeeclipse-che/ed136540-c650-47a2-918b-bb7f6020469d    running        z7  10.30.56.54    vm-5a3a2b10-d0c9-47c8-97f0-6ea64c339df8  large    true                                           115.68.46.178mariadb/ec34aa5b-c7cc-4297-9e2d-babf05d83832        running        z3  10.30.56.55    vm-9e1631af-b6c8-481e-aad3-3fd713f106a9  small    truewebide-broker/a641df99-d36a-49ee-8329-018fe10fa23d  running        z3  10.30.56.56    vm-eb784964-48cd-4e4c-b080-53675d3738c2  medium   true​3 vms​Succeeded
```

## 3. WEB-IDE의 PaaS-TA 포털사이트 연동 <a id="3-web-ide-paas-ta"></a>

### 3.1. WEB-IDE 서비스 브로커 등록 <a id="3-1-web-ide"></a>

> `$ cf create-service-broker {서비스팩 이름} {서비스팩 사용자ID} {서비스팩 사용자비밀번호} http://{서비스팩 URL(IP)}`

**서비스팩 이름** : 서비스 팩 관리를 위해 PaaS-TA에서 보여지는 명칭이다. 서비스 Marketplace에서는 각각의 API 서비스 명이 보여지니 여기서 명칭은 서비스팩 리스트의 명칭이다. **서비스팩 사용자ID** / 비밀번호 : 서비스팩에 접근할 수 있는 사용자 ID입니다. 서비스팩도 하나의 API 서버이기 때문에 아무나 접근을 허용할 수 없어 접근이 가능한 ID/비밀번호를 입력한다. **서비스팩 URL** : 서비스팩이 제공하는 API를 사용할 수 있는 URL을 입력한다.

> `$ cf create-service-broker webide-service-broker admin cloudfoundry http://10.30.56.56:8080`
>
> ```text
> $ cf create-service-broker webide-service-broker admin cloudfoundry http://10.30.56.56:8080Creating service broker webide-service-broker as admin...OK
> ```

**등록된 WEB-IDE 서비스 브로커를 확인한다.**

> `$ cf service-brokers`

```text
$ cf service-brokersGetting service brokers as admin...​name                          urlwebide-service-broker         http://10.30.56.56:8080
```

#### 접근 가능한 서비스 목록을 확인한다. <a id="undefined"></a>

> `$ cf service-access`

```text
$ cf service-accessGetting service access as admin...broker: webide-service-broker   service   plan            access   orgs   webide    webide-shared   none
```

* 서비스 브로커 등록시 최초에는 접근을 허용하지 않는다. 따라서 access는 none으로 설정된다.

#### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. \(전체 조직\) <a id="undefined-1"></a>

> `$ cf enable-service-access webide` `$ cf service-access`

```text
$ cf enable-service-access webideEnabling access to all plans of service webide for all orgs as admin...OK​$ cf service-accessGetting service access as admin...​broker: webide-service-broker   service   plan            access   orgs   webide    webide-shared   all
```

#### PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다. <a id="paas-ta-marketplace"></a>

> `$ cf marketplace`

```text
$ cf marketplaceGetting services from marketplace in org system / space dev as admin...OK​service                  plans                                   description                                                                                                                              brokerwebide                   webide-shared                           A paasta web ide service for application development.provision parameters                                                                webide-service-broker
```

#### Marketplace에서 원하는 서비스가 있으면 서비스 신청\(Provision\)을 한다. <a id="marketplace-provision"></a>

> `$ cf create-service {서비스명} {서비스 플랜} {내 서비스명}`
>
> * **서비스명** : webide로 Marketplace에서 보여지는 서비스 명칭이다.
> * **서비스플랜** : 서비스에 대한 정책으로 plans에 있는 정보 중 하나를 선택한다. webide 서비스는 standard plan만 지원한다.
> * **내 서비스명** : 내 서비스에서 보여지는 명칭이다. 이 명칭을 기준으로 환경 설정 정보를 가져온다.
>
> `$ cf create-service webide webide-shared webide-service`

```text
$ cf create-service webide webide-shared paasta-webide-serviceCreating service instance paasta-webide-service in org system / space dev as admin...OK
```

#### 생성된 WEB-IDE 서비스 인스턴스를 확인한다. <a id="web-ide"></a>

> `$ cf services`

```text
$ cf servicesGetting services in org system / space dev as admin...​name                    service      plan            bound apps   last operation     broker                    upgrade availablepaasta-webide-service   webide       webide-shared                create succeeded   webide-service-broker
```

## 4. WEB-IDE 에서 CF CLI 사용법 <a id="4-web-ide-cf-cli"></a>

### 4.1. WEB-IDE New Project 화면 <a id="4-1-web-ide-new-project"></a>

_**※**_ [_**PaaS-TA 운영자 포탈 4.3.3 카탈로그 관리 서비스 가이드**_](https://github.com/paas-ta0812/test-5-5-0/tree/dfea4c50da10ef7a223130568dd829b87c27a69c/use-guide/portal/PAAS-TA_ADMIN_PORTAL_USE_GUIDE_V1.1.md#4.3.3) _**참고**_

* 사용할 언어를 선택하고 Create workspace and project 로 새로운 프로젝트를 시작한다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F6bfd9841124b01b794a15e2210ce442edaf83c17.png?alt=media)

* Workspace를 구성하기 위해 Docker 관련 자료를 다운로드한다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fac3bdbcbca993078db8cfee11c7baad50ac5b95a.png?alt=media)

### 4.2. WEB-IDE Workspace 화면 <a id="4-2-web-ide-workspace"></a>

* Open Project를 누르면 Workspace 화면이 열린다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F5b9b719b5e6d54875c3ab51beb13f77d38f8b020.png?alt=media)

* 실제로 소스를 개발해서 빌드하거나 GIT이나 SVN에서 IMPORT 한다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F5f9f24343704f48598c9f93b1ddc10c9e7783e7c.png?alt=media)

### 4.3. WEB-IDE Teminal에서의 CF CLI 실행 <a id="4-3-web-ide-teminal-cf-cli"></a>

**-cf api 명령을 이용해 endpoint를 지정한다.**

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fca1598b4fd80d7efa328a9bd12cdf1e2c140e56c.png?generation=1615181694494641&alt=media)​

**cf login 명령어로 로그인하고 조직과 공간을 선택한다.**

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F0bc4205235619eaa294ad75502d8b16e8d76d234.png?generation=1615181691596434&alt=media)​

**cf push 를 이용해 cf에 앱을 업로드한다.**

> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F9829c8b29dad20e226fb358ac04aeef6f5f54268.png?generation=1615181786979755&alt=media)​

