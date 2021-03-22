# Cubrid 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](cubrid.md#1) 1.1. [목적](cubrid.md#1.1) 1.2. [범위](cubrid.md#1.2) 1.3. [시스템 구성도](cubrid.md#1.3) 1.4. [참고자료](cubrid.md#1.4)​
2. 3. 4. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서\(Cubrid 서비스팩 설치 가이드\)는 전자정부표준프레임워크 기반의 PaaS-TA에서 제공되는 서비스팩인 Cubrid 서비스팩을 Bosh를 이용하여 설치 하는 방법과 PaaS-TA의 SaaS 형태로 제공하는 Application 에서 Cubrid 서비스를 사용하는 방법을 기술하였다. PaaS-TA 3.5 버전부터는 Bosh2.0 기반으로 deploy를 진행하며 기존 Bosh1.0 기반으로 설치를 원할경우에는 PaaS-TA 3.1 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 Cubrid 서비스팩을 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

본 문서의 설치된 시스템 구성도이다. Cubrid Server, Cubrid 서비스 브로커로 최소사항을 구성하였다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F4cf08f16bdb94268290f2fd2a4a4d877d8bfc494.png?alt=media)

시스템 구성도

* 설치할때 cloud config에서 사용하는 VM\_Tpye명과 스펙
* 각 Instance의 Resource Pool과 스펙

### 1.4. 참고자료 <a id="1-4"></a>

​[http://bosh.io/docs](http://bosh.io/docs) [http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)​

## 2. Cubrid 서비스 설치 <a id="2-cubrid"></a>

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

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/cubrid/vars.yml

```text
# STEMCELLstemcell_os: "ubuntu-xenial"             # stemcell osstemcell_version: "621.94"               # stemcell version​# NETWORKprivate_networks_name: "default"         # private network name​# CUBRIDcubrid_azs: [z5]                         # cubrid azscubrid_instances: 1                      # cubrid instances (1)cubrid_vm_type: "medium"                 # cubrid vm type​# CUBRID BROKERcubrid_broker_azs: [z5]                  # cubrid broker azscubrid_broker_instances: 1               # cubrid broker instances (1)cubrid_broker_vm_type: "minimal"         # cubrid broker vm type​# CUBRID ACCESS INFOcubrid_max_clients: 200                  # cubrid access max clientscubrid_db_port: 30000                    # cubrid portcubrid_db_name: "cubrid_broker"          # cubrid service 관리를 위한 데이터베이스 이름cubrid_db_user: "dba"                    # 브로커 관리용 데이터베이스 접근 사용자이름cubrid_db_passwd: "paasta"               # 브로커 관리용 데이터베이스 접근 사용자 비밀번호cubrid_ssh_port: 22                      # cubrid가 설치된 서버 SSH 접속 포트cubrid_ssh_user: "vcap"                  # cubrid가 설치된 서버 SSH 접속 사용자 이름
```

### 2.5. 서비스 설치 <a id="2-5"></a>

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고, Option file을 추가할지 선택한다.

   \(선택\) -o operations/use-compiled-releases.yml \(ubuntu-xenial/621.94로 컴파일 된 릴리즈 사용\)

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/cubrid/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d cubrid deploy --no-redact cubrid.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/cubrid    $ sh ./deploy.sh
```

### 2.6. 서비스 설치 - 다운로드 된 PaaS-TA Release 파일 이용 방식 <a id="2-6-paas-ta-release"></a>

* 서비스 설치에 필요한 릴리즈 파일을 다운로드 받아 Local machine의 서비스 설치 작업 경로로 위치시킨다.
  * 

```text
# 릴리즈 다운로드 파일 위치 경로 생성$ mkdir -p ~/workspace/paasta-5.5.1/release/service​# 릴리즈 파일 다운로드 및 파일 경로 확인$ ls ~/workspace/paasta-5.5.1/release/servicepaasta-cubrid-2.0.1.tgz
```

* 서버 환경에 맞추어 Deploy 스크립트 파일의 VARIABLES 설정을 수정하고 Option file 및 변수를 추가한다.

   \(추가\) -o operations/use-offline-releases.yml \(미리 다운받은 offline 릴리즈 사용\)

   \(추가\) -v releases\_dir=""

> $ vi ~/workspace/paasta-5.5.1/deployment/service-deployment/cubrid/deploy.sh

```text
#!/bin/bash​# VARIABLESCOMMON_VARS_PATH=""    # common_vars.yml File Path (e.g. ../../common/common_vars.yml)BOSH_ENVIRONMENT="${BOSH_ENVIRONMENT}"        # bosh director alias name (PaaS-TA에서 제공되는 create-bosh-login.sh 미 사용시 bosh envs에서 이름을 확인하여 입력)​# DEPLOYbosh -e ${BOSH_ENVIRONMENT} -n -d cubrid deploy --no-redact cubrid.yml \    -l ${COMMON_VARS_PATH} \    -l vars.yml \    -v releases_dir="/home/ubuntu/workspace/paasta-5.5.1/release"
```

* 서비스를 설치한다.

```text
$ cd ~/workspace/paasta-5.5.1/deployment/service-deployment/cubrid  $ sh ./deploy.sh
```

### 2.7. 서비스 설치 확인 <a id="2-7"></a>

설치 완료된 서비스를 확인한다.

> $ bosh -e micro-bosh -d cubrid vms

```text
Using environment '10.0.1.6' as client 'admin'​Task 6424. Done​Deployment 'cubrid'​Instance                                            Process State  AZ  IPs            VM CID                                   VM Type  Active  cubrid/e60ec9e8-4834-4655-a1f0-f8e2468d0ff5         running        z5  10.30.101.0  vm-1a5805f4-8e7b-4e3e-bf17-270976d2b566  medium   true  cubrid_broker/4e887b24-c621-492f-bd92-f061f7c0ce80  running        z5  10.30.101.1  vm-77133451-0d7a-4be0-9d24-ae094ca12ea9  minimal  true ​2 vms​Succeeded
```

## 3. Cubrid연동 Sample App 설명 <a id="3-cubrid-sample-app"></a>

본 Sample Web App은 PaaS-TA에 배포되며 Cubrid의 서비스를 Provision과 Bind를 한 상태에서 사용이 가능하다.

### 3.1. 서비스 브로커 등록 <a id="3-1"></a>

Cubrid 서비스팩 배포가 완료 되었으면 Application에서 서비스 팩을 사용하기 위해서 먼저 Cubrid 서비스 브로커를 등록해 주어야 한다. 서비스 브로커 등록시 PaaS-TA에서 서비스브로커를 등록할 수 있는 사용자로 로그인이 되어있어야 한다.

#### 서비스 브로커 목록을 확인한다. <a id="undefined"></a>

> `cf service-brokers`

```text
$ cf service-brokersGetting service brokers as admin...​name   urlNo service brokers found
```

#### Cubrid 서비스 브로커를 등록한다. <a id="cubrid"></a>

> `$ cf create-service-broker [SERVICE_BROKER] [USERNAME] [PASSWORD] [SERVICE_BROKER_URL]`
>
> * \[SERVICE\_BROKER\] : 서비스 브로커 명
> * \[USERNAME\] / \[PASSWORD\] : 서비스 브로커에 접근할 수 있는 사용자 ID / PASSWORD
> * \[SERVICE\_BROKER\_URL\] : 서비스 브로커 접근 URL
>
> `cf create-service-broker cubrid-service-broker admin cloudfoundry http://10.30.101.1:8080`

```text
$ cf create-service-broker cubrid-service-broker admin cloudfoundry http://10.30.101.1:8080Creating service broker cubrid-service-broker as admin...OK
```

#### 등록된 Cubrid 서비스 브로커를 확인한다. <a id="cubrid-1"></a>

> `$ cf service-brokers`

```text
$ cf service-brokersGetting service brokers as admin...​name                      urlcubrid-service-broker     http://10.30.101.1:8080
```

* 접근 가능한 서비스 목록을 확인한다.

> `$ cf service-access`

```text
$ cf service-accessGetting service access as admin...broker: cubrid-service-broker   service    plan    access   orgs   CubridDB   utf8    none   CubridDB   euckr   none
```

#### 서비스 브로커 생성시 디폴트로 접근을 허용하지 않는다. <a id="undefined-1"></a>

#### 특정 조직에 해당 서비스 접근 허용을 할당하고 접근 서비스 목록을 다시 확인한다. \(전체 조직\) <a id="undefined-2"></a>

> `$ cf enable-service-access CubridDB` `$ cf service-access`

```text
$ cf enable-service-access CubridDBEnabling access to all plans of service CubridDB for all orgs as admin...OK​$ cf service-accessGetting service access as admin...broker: cubrid-service-broker   service    plan    access   orgs   CubridDB   utf8    all   CubridDB   euckr   all
```

### 3.2. Sample App 구조 <a id="3-2-sample-app"></a>

Sample Web App은 PaaS-TA에 App으로 배포가 된다. App을 배포하여 구동시 Bind 된 Cubrid 서비스 연결정보로 접속하여 초기 데이터를 생성하게 된다. 배포 완료 후 정상적으로 App 이 구동되면 브라우져나 curl로 해당 App에 접속 하여 Cubrid 환경정보\(서비스 연결 정보\)와 초기 적재된 데이터를 보여준다.

Sample Web App 구조는 다음과 같다.

#### PaaS-TA-Sample-Apps을 다운로드 받고 Service 폴더안에 있는 Cubrid Sample Web App인 hello-spring-cubrid를 복사한다. <a id="paas-ta-sample-apps-service-cubrid-sample-web-app-hello-spring-cubrid"></a>

> `$ ls -all`
>
> ​![](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fa2420dde0a34b80c922e17b0fe0d2a7cdb8939c7.png?generation=1615877357330229&alt=media)​

### 3.3. PaaS-TA에서 서비스 신청 <a id="3-3-paas-ta"></a>

Sample Web App에서 Cubrid 서비스를 사용하기 위해서는 서비스 신청\(Provision\)을 해야 한다. \(참고: 서비스 신청시 PaaS-TA에서 서비스를신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.\)

#### 먼저 PaaS-TA Marketplace에서 서비스가 있는지 확인을 한다. <a id="paas-ta-marketplace"></a>

> `$ cf marketplace`

```text
$ cf marketplaceGetting services from marketplace in org demo / space dev as demo...OK​service    plans         descriptionCubridDB   utf8, euckr   A simple cubrid implementation​TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service.
```

#### Marketplace에서 원하는 서비스가 있으면 서비스 신청\(Provision\)을 한다. <a id="marketplace-provision"></a>

> `$ cf create-service {서비스명} {서비스플랜} {내서비스명}`

**서비스명** CubridDB로 Marketplace에서 보여지는 서비스 명칭이다. **서비스플랜** 서비스에 대한 정책으로 plans에 있는 정보 중 하나를 선택한다. Cubrid 서비스는 100mb, 1gb를 지원한다. **내서비스명**내 서비스에서 보여지는 명칭이다. 이 명칭을 기준으로 환경설정정보를 가져온다.

> `$ cf create-service CubridDB utf8 cubrid-service-instance`

```text
$ cf create-service CubridDB utf8 cubrid-service-instanceCreating service instance cubrid-service-instance in org demo / space dev as demo...OK
```

#### 생성된 Cubrid 서비스 인스턴스를 확인한다. <a id="cubrid-2"></a>

> `$ cf services`

```text
$ cf servicesGetting services in org demo / space dev as demo...OK​name                      service    plan   bound apps   last operationcubrid-service-instance   CubridDB   utf8                create succeeded
```

### 3.4. Sample App에 서비스 바인드 신청 및 App 확인 <a id="3-4-sample-app-app"></a>

서비스 신청이 완료되었으면 Sample Web App 에서는 생성된 서비스 인스턴스를 Bind 하여 App에서 Cubrid 서비스를 이용한다. \*참고: 서비스 Bind 신청시 PaaS-TA에서 서비스 Bind신청 할 수 있는 사용자로 로그인이 되어 있어야 한다.

#### Sample Web App 디렉토리로 이동하여 manifest 파일을 확인한다. <a id="sample-web-app-manifest"></a>

다운로드 :: [http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download](http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download)​

```text
$ wget -O sample.zip http://45.248.73.44/index.php/s/x8Tg37WDFiL5ZDi/download$ unzip sample.zip -d sample$ cd sample/Service/hello-spring-cubrid
```

> `$ vi manifest.yml`

```text
applications:- name: hello-spring-cubrid # 배포할 App 이름  memory: 1024M # 배포시 메모리 사이즈  instances: 1 # 배포 인스턴스 수  path: target/hello-spring-cubrid-1.0.0-BUILD-SNAPSHOT.war #배포하는 App 파일 PATH
```

* 참고: ./build/libs/hello-spring-cubrid.war 파일이 존재 하지 않을 경우 gradle 빌드를 수행 하면 파일이 생성된다.

#### --no-start 옵션으로 App을 배포한다. <a id="no-start-app"></a>

#### --no-start: App 배포시 구동은 하지 않는다. <a id="no-start-app-1"></a>

> `$ cf push --no-start`

```text
$ cf push --no-startUsing manifest file D:\sample-app\hello-spring-cubrid\manifest.yml​Creating app hello-spring-cubrid in org demo / space dev as demo...OK​Creating route hello-spring-cubrid.115.68.47.178.xip.io...OK​Binding hello-spring-cubrid.115.68.47.178.xip.io to hello-spring-cubrid...OK​Uploading hello-spring-cubrid...Uploading app files from: C:\Users\demo\AppData\Local\Temp\unzipped-app845663023Uploading 7.2M, 59 filesDone uploadingOK
```

#### 배포된 Sample App을 확인하고 로그를 수행한다. <a id="sample-app"></a>

> `$ cf apps`

```text
$ cf appsGetting apps in org demo / space dev as demo...OK​name                  requested state   instances   memory   disk   urlshello-spring-cubrid   stopped           0/1         1G       1G     hello-spring-cubrid.115.68.47.178.xip.iosampleapp             started           1/1         1G       1G     sampleapp.115.68.47.178.xip.io
```

> `$ cf logs {배포된 App명}` `$ cf logs hello-spring-cubrid`

```text
$ cf logs hello-spring-cubridRetrieving logs for app hello-spring-cubrid in org demo / space dev as demo...
```

**Sample Web App에서 생성한 서비스 인스턴스 바인드 신청을 한다.**

> `$ cf bind-service hello-spring-cubrid cubrid-service-instance`

```text
$ cf bind-service hello-spring-cubrid cubrid-service-instanceBinding service cubrid-service-instance to app hello-spring-cubrid in org demo / space dev as demo...OKTIP: Use 'cf restage hello-spring-cubrid' to ensure your env variable changes take effect
```

#### 바인드가 적용되기 위해서 App을 재기동한다. <a id="app"></a>

> `$ cf restart hello-spring-cubrid`

```text
$ cf restart hello-spring-cubrid​Starting app hello-spring-cubrid in org demo / space dev as demo...Downloading binary_buildpack...Downloading java_buildpack...Downloading go_buildpack...Downloading nodejs_buildpack...Downloading php_buildpack...Downloaded php_buildpack (301.5K)Downloading nginx_buildpack...Downloading r_buildpack...Downloaded java_buildpack (224.7K)Downloaded go_buildpack (5M)Downloading python_buildpack...Downloaded nodejs_buildpack (5.1M)Downloading ruby_buildpack...Downloaded r_buildpack (4.8M)Downloading dotnet_core_buildpack...Downloaded binary_buildpack (8.2M)Downloaded nginx_buildpack (8.2M)Downloading staticfile_buildpack...Downloaded python_buildpack (5M)Downloaded ruby_buildpack (5M)Downloaded dotnet_core_buildpack (5.3M)Downloaded staticfile_buildpack (5.6M)Cell 215698eb-793e-4271-80e0-2fd7962f93b6 creating container for instance abd243fa-552d-49d4-81f8-685dc26af007Cell 215698eb-793e-4271-80e0-2fd7962f93b6 successfully created container for instance abd243fa-552d-49d4-81f8-685dc26af007Downloading app package...Downloaded app package (7.6M)-----> Java Buildpack v4.19.1 | https://github.com/cloudfoundry/java-buildpack.git#3f4eee2-----> Downloading Jvmkill Agent 1.16.0_RELEASE from https://java-buildpack.cloudfoundry.org/jvmkill/bionic/x86_64/jvmkill-1.16.0-RELEASE.so (0.3s)-----> Downloading Open Jdk JRE 1.8.0_212 from https://java-buildpack.cloudfoundry.org/openjdk/bionic/x86_64/openjdk-jre-1.8.0_212-bionic.tar.gz (1.2s)       Expanding Open Jdk JRE to .java-buildpack/open_jdk_jre (1.3s)       JVM DNS caching disabled in lieu of BOSH DNS caching-----> Downloading Open JDK Like Memory Calculator 3.13.0_RELEASE from https://java-buildpack.cloudfoundry.org/memory-calculator/bionic/x86_64/memory-calculator-3.13.0-RELEASE.tar.gz (0.1s)       Loaded Classes: 10196, Threads: 250-----> Downloading Client Certificate Mapper 1.8.0_RELEASE from https://java-buildpack.cloudfoundry.org/client-certificate-mapper/client-certificate-mapper-1.8.0-RELEASE.jar (0.0s)-----> Downloading Container Security Provider 1.16.0_RELEASE from https://java-buildpack.cloudfoundry.org/container-security-provider/container-security-provider-1.16.0-RELEASE.jar (5.1s)-----> Downloading Spring Auto Reconfiguration 2.7.0_RELEASE from https://java-buildpack.cloudfoundry.org/auto-reconfiguration/auto-reconfiguration-2.7.0-RELEASE.jar (0.4s)-----> Downloading Tomcat Instance 9.0.19 from https://java-buildpack.cloudfoundry.org/tomcat/tomcat-9.0.19.tar.gz (0.3s)       Expanding Tomcat Instance to .java-buildpack/tomcat (0.2s)-----> Downloading Tomcat Access Logging Support 3.3.0_RELEASE from https://java-buildpack.cloudfoundry.org/tomcat-access-logging-support/tomcat-access-logging-support-3.3.0-RELEASE.jar (0.1s)-----> Downloading Tomcat Lifecycle Support 3.3.0_RELEASE from https://java-buildpack.cloudfoundry.org/tomcat-lifecycle-support/tomcat-lifecycle-support-3.3.0-RELEASE.jar (5.0s)-----> Downloading Tomcat Logging Support 3.3.0_RELEASE from https://java-buildpack.cloudfoundry.org/tomcat-logging-support/tomcat-logging-support-3.3.0-RELEASE.jar (0.1s)Exit status 0Uploading droplet, build artifacts cache...Uploading droplet...Uploading build artifacts cache...Uploaded build artifacts cache (53.7M)Uploaded droplet (60.1M)Uploading completeCell 215698eb-793e-4271-80e0-2fd7962f93b6 stopping instance abd243fa-552d-49d4-81f8-685dc26af007Cell 215698eb-793e-4271-80e0-2fd7962f93b6 destroying container for instance abd243fa-552d-49d4-81f8-685dc26af007Cell 215698eb-793e-4271-80e0-2fd7962f93b6 successfully destroyed container for instance abd243fa-552d-49d4-81f8-685dc26af007​0 of 1 instances running, 1 starting0 of 1 instances running, 1 starting0 of 1 instances running, 1 starting0 of 1 instances running, 1 starting1 of 1 instances running​App started​OK​App hello-spring-cubrid was started using this command `JAVA_OPTS="-agentpath:$PWD/.java-buildpack/open_jdk_jre/bin/jvmkill-1.16.0_RELEASE=printHeapHistogram=1 -Djava.io.tmpdir=$TMPDIR -XX:ActiveProcessorCount=$(nproc) -Djava.ext.dirs=$PWD/.java-buildpack/container_security_provider:$PWD/.java-buildpack/open_jdk_jre/lib/ext -Djava.security.properties=$PWD/.java-buildpack/java_security/java.security $JAVA_OPTS -Daccess.logging.enabled=false -Dhttp.port=$PORT" && CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-3.13.0_RELEASE -totMemory=$MEMORY_LIMIT -loadedClasses=12719 -poolType=metaspace -stackThreads=250 -vmOptions="$JAVA_OPTS") && echo JVM Memory Configuration: $CALCULATED_MEMORY && JAVA_OPTS="$JAVA_OPTS $CALCULATED_MEMORY" && MALLOC_ARENA_MAX=2 JAVA_OPTS=$JAVA_OPTS JAVA_HOME=$PWD/.java-buildpack/open_jdk_jre exec $PWD/.java-buildpack/tomcat/bin/catalina.sh run`​Showing health and status for app hello-spring-cubrid in org demo / space dev as demo...OK​requested state: startedinstances: 1/1usage: 1G x 1 instancesurls: hello-spring-cubrid.115.68.47.178.xip.iolast uploaded: Mon Nov 18 08:47:44 UTC 2019stack: cflinuxfs3buildpack: client-certificate-mapper=1.8.0_RELEASE container-security-provider=1.16.0_RELEASE java-buildpack=v4.19.1-https://github.com/cloudfoundry/java-buildpack.git#3f4eee2 java-opts java-security jvmkill-agent=1.16.0_RELEASE open-jdk-like-jre=1.8.0_2...​     state     since                    cpu    memory       disk           details#0   running   2019-11-18 06:10:42 PM   0.0%   125M of 1G   128.8M of 1G
```

* \(참고\) 바인드 후 App구동시 Cubrid 서비스 접속 에러로 App 구동이 안될 경우 보안 그룹을 추가한다.

```text
##### rule.json 화일을 만들고 아래와 같이 내용을 넣는다.$ vi rule.json[  {    "protocol": "tcp",    "destination": "10.30.101.0",    "ports": "30000"  }]​##### 보안 그룹을 생성한 후, 모든 App에 Cubrid 서비스를 사용할수 있도록 생성한 보안 그룹을 적용한다.$ cf create-security-group CubridDB rule.json$ cf bind-running-security-group CubridDB​##### App을 리부팅 한다.$ cf restart hello-spring-cubrid
```

#### App이 정상적으로 Cubrid 서비스를 사용하는지 확인한다. <a id="app-cubrid"></a>

#### curl 로 확인 <a id="curl"></a>

> `$ curl hello-spring-cubrid.115.68.47.178.xip.io`

```text
$ curl hello-spring-cubrid.115.68.47.178.xip.io​​        Hello Spring Cubrid        Hello Spring Cubrid!​        Database Info:        DataSource: jdbc:cubrid:10.30.101.0::bd545a9031ffed9f:d795c4b014d3c5ce:3e114ab842f46dff:
​        Current States:​​                                State [id=1, stateCode=MA, name=Massachusetts]
​                                State [id=2, stateCode=NH, name=New Hampshire]
​                                State [id=3, stateCode=ME, name=Maine]
​                                State [id=4, stateCode=VT, name=Vermont]
​​​
```

#### 브라우져에서 확인 <a id="undefined-3"></a>

> ​![3-3-8-1](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F18ce0388715f39bf4f4af3a203635b771f1a8fa4.png?generation=1615171124798870&alt=media)​

## 4. Cubrid Client 툴 접속 <a id="4-cubrid-client"></a>

Application에 바인딩된 Cubrid 서비스 연결정보는 Private IP로 구성되어 있기 때문에 Cubrid Client 툴에서 직접 연결할수 없다. 따라서 Cubrid Client 툴에서 SSH 터널, Proxy 터널 등을 제공하는 툴을 사용해서 연결하여야 한다. 본 가이드는 무료 SSH 및 텔넷 접속 툴인 Putty를 이용하여 SSH 터널을 통해 연결 하는 방법을 제공하며 Cubrid Client 툴로써는 Cubrid에서 제공하는 Cubrid Manager로 가이드한다. Cubrid Manager 에서 접속하기 위해서 먼저 SSH 터널링 할수 있는 VM 인스턴스를 생성해야한다. 이 인스턴스는 SSH로 접속이 가능해야 하고 접속 후 Open PaaS 에 설치한 서비스팩에 Private IP 와 해당 포트로 접근이 가능하도록 시큐리티 그룹을 구성해야 한다. 이 부분은 vSphere관리자 및 OpenPaaS 운영자에게 문의하여 구성한다.

### 4.1. Putty 다운로드 및 터널링 <a id="4-1-putty"></a>

Putty 프로그램은 SSH 및 텔넷 접속을 할 수 있는 무료 소프트웨어이다.

* Putty를 다운로드 하기 위해 아래 URL로 이동하여 파일을 다운로드 한다. 별도의 설치과정없이 사용할 수 있다.

  ​[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

  ​![4-1-0-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fcfcc5404e7d50088cc5ffe4094e5edeebc4fc14c.png?generation=1615177198138185&alt=media)​

#### 다운받은 putty.exe.파일을 더블클릭하여 실행한다. <a id="putty-exe"></a>

> ​![4-1-1-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F19677ed2a618bd4b05881ec3f37376bbbe556b23.png?generation=1615177202146147&alt=media)​

#### Session 탭의 Host name과 Port란에. OpenPaaS 운영 관리자에게 제공받은 SSH 터널링 가능한 서버 정보를 입력한다. <a id="session-host-name-port-openpaas-ssh"></a>

> ​![4-1-2-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff0411dec23d98c753a8f169f8ae73fb4a6cb2d37.png?generation=1615177207143473&alt=media)​

#### Connection-&gt;SSH-&gt;Tunnels 탭에서 Source port\(내 로컬에서 접근할 포트\), Destination\(터널링으로 연결할 서버정보\)를 입력하고 Local, Auto를 선택 후 Add를 클릭한다. <a id="connection-greater-than-ssh-greater-than-tunnels-source-port-destination-local-auto-add"></a>

​![4-1-3-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F8cbabb05d3f95b432d21068fa2213e93286b24e7.png?generation=1615177220674051&alt=media) ![4-1-3-1](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fa6125de4999a16b49b6c760c8f99406fc7e90893.png?generation=1615177199120391&alt=media) 서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env 명령어로 이용하여 확인한다. 예\) $ cf env hello-spring-cubrid

> ​![4-1-4-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F9618d9d54a6c409dfc1458620d3bf960020c129f.png?generation=1615177215173422&alt=media)​

#### Session 탭에서 Saved Sessions에 저장할 이름을 입력하고 Save를 눌러 저장한 후 Open버튼을 누른다. <a id="session-saved-sessions-save-open"></a>

> ​![4-1-5-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F83315855673d5162621e7261ade38600e2ccf1aa.png?generation=1615177209084373&alt=media)​

#### 서버 접속정보를 입력하여 접속하여 터널링을 완료한다. <a id="undefined-4"></a>

만약 ssh 인증이 Password방식이 아닌 Key인증 방식일 경우, Connection-&gt;SSH-&gt;인증탭의 '인증 개인키 파일'에 key 파일을 등록하여 인증한다. Key파일의 확장자가 .pem이라면 putty설치시 같이 설치된 puttygen을 사용하여 ppk파일로 변환한뒤 사용한다.

> ​![4-1-6-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fc07276ebc2352c8a0396b8d32af68eec3b0bb4a4.png?generation=1615177218093028&alt=media)​

### 4.2. Cubrid Manager 설치 & 연결 <a id="4-2-cubrid-manager-and"></a>

Cubrid Manager 프로그램은 Cubrid에서 제공하는 무료로 사용할 수 있는 소프트웨어이다.

* Cubrid Manager를 다운로드 하기 위해 아래 URL로 이동하여 설치파일을 다운로드 한다.

  ​[http://ftp.cubrid.org/CUBRID\_Tools/CUBRID\_Manager/](http://ftp.cubrid.org/CUBRID_Tools/CUBRID_Manager/)

  ​![4-2-0-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F7766aff055916e4c8500e4d41bcb4a17df453d65.png?generation=1615177216169442&alt=media)​

#### 다운받은 파일을 더블클릭하여 실행한다. <a id="undefined-5"></a>

> ​![4-2-1-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff0150dadf3fa1d15cb680953b0429babdbba8153.png?generation=1615177200056306&alt=media)​

#### 한국어를 선택하고 OK를 누른다. <a id="ok"></a>

> ​![4-2-2-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F0b8ee686945de51a5edbe1be690e7af02df9d7bc.png?generation=1615177201111109&alt=media)​

#### 다음을 눌러 계속 진행한다. <a id="undefined-6"></a>

> ​![4-2-3-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F439428a6ba5eb0023de6c62e6d3afa2ef855d900.png?generation=1615177205142042&alt=media)​

#### 동의함을 눌러 계속 진행한다. <a id="undefined-7"></a>

> ​![4-2-4-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fd82452cbd08d1a1994bd95f891a08ab432e682d6.png?generation=1615177204143408&alt=media)​

#### 바로가기 옵션을 선택 후 다음을 눌러 계속 진행한다. <a id="undefined-8"></a>

> ​![4-2-5-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff3463b5791cde9dcbcb7dc685d3ce525490cc263.png?generation=1615177217115907&alt=media)​

#### 설치 경로를 입력하고 설치를 눌러 설치를 시작한다. <a id="undefined-9"></a>

> ​![4-2-6-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F435626b87055dbeb1c0bf7ea304007eb97e6a6a4.png?generation=1615177219113770&alt=media)​

#### 설치가 완료되면 다음을 눌러 계속 진행한다. <a id="undefined-10"></a>

> ​![4-2-7-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F5c8e2e2f3ff4fe83320dd33b43ed65f8f7bf4725.png?generation=1615177220652287&alt=media)​

#### 마침을 눌러 설치를 완료한다. <a id="undefined-11"></a>

> ​![4-2-8-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F02721b34e61b29a6ca2da687b9966a4e5fa43998.png?generation=1615177212040936&alt=media)​

#### 설치된 Cubrid Manager를 실행하면 처음 나오는 화면이다. Workspace를 선택 후 OK를 눌러 실행한다. 만약 이 창을 다시보기를 원치않는다면 '기본적으로 이것을 사용하고 다시 물어 보지 않기' 옵션을 선택한다. <a id="cubrid-manager-workspace-ok"></a>

> ​![4-2-9-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F8cf922114ce99fd476c62bec0d45b2c10ca8ab73.png?generation=1615177211130717&alt=media)​

#### 관리 모드, 질의 모드 둘중 목적에 맞게 선택 후 확인을 눌러 실행한다. 여기서는 질의 모드로 실행한다. <a id="undefined-12"></a>

> ​![4-2-10-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Ff1a35ceb5214bcdc9eb9a075a7c3f22f92f3933f.png?generation=1615177214101077&alt=media)​

#### 연결정보를 입력하기 위해서 연결 정보 등록을 누른다. <a id="undefined-13"></a>

> ​![4-2-11-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F7a5f08218bffbd497acb77eeee10144e285322d6.png?generation=1615177213201178&alt=media)​

#### Server에 접속하기 위한 Connection 정보를 입력한다. <a id="server-connection"></a>

> ​![4-2-12-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F2f0243cfcdc3e4798e22822e47649f6dd2f0ca15.png?generation=1615177221133612&alt=media)​

#### 서버 정보는 Application에 바인드되어 있는 서버 정보를 입력한다. cf env 명령어로 이용하여 확인한다. <a id="application-cf-env"></a>

> `cf env hello-spring-cubrid`
>
> ​![4-2-13-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F57d94adcf7312a29382f752687a279eece32c734.png?generation=1615177220842849&alt=media)​

#### 연결 테스트 버튼을 클릭하여 접속 테스트를 한다. <a id="undefined-14"></a>

> ​![4-2-14-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F865fee1713de59b42a9b8211c4ff33abf38cb10b.png?generation=1615177206128033&alt=media)​

#### 정보가 정상적으로 입력되었다면 '연결이 성공하였습니다.'라는 메시지가 나온다. <a id="undefined-15"></a>

확인 버튼을 눌러 창을 닫는다.

> ​![4-2-15-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F5e176dc75fc60d49fd6c0883f6a43a24b4753339.png?generation=1615177220102351&alt=media)​

#### 연결 버튼을 클릭하여 접속한다 <a id="undefined-16"></a>

> ​![4-2-16-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F0c874f39d5f8e7a301a93c6185178039082dcf71.png?generation=1615177210178746&alt=media)​

#### 접속이 완료되면 좌측에 스키마 정보가 나타난다. <a id="undefined-17"></a>

> ​![4-2-17-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2Fe1815b6aa826518d08c1133f3cb42a9cc657423e.png?generation=1615177208355930&alt=media)​

#### 질의 편집기 버튼을 클릭하면 오른쪽 창에 query를 입력할 수 있는 창이 열린다. <a id="query"></a>

> ​![4-2-18-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F8705b35fb76236ac858d38aa4888fa3facd9a3be.png?generation=1615177221231389&alt=media)​

#### 우측 화면에 쿼리 항목에 Query문을 작성한 후 실행 버튼\(삼각형\)을 클릭한다. 쿼리문에 이상이 없다면 정상적으로 결과를 얻을 수 있을 것이다. <a id="query-1"></a>

> ​![4-2-19-0](https://firebasestorage.googleapis.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F37ec31a2d54db262a0ffcd60f8fa2374f173dc26.png?generation=1615177203293313&alt=media)​

