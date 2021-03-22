# PaaS-TA 5.5.0 가이드 문서

#### 릴리즈의 경로가 [http://45.248.73.44/](http://45.248.73.44/) 에서 [https://nextcloud.paas-ta.org/](https://nextcloud.paas-ta.org/) 로 변경되었습니다 <a id="http-45-248-73-44-https-nextcloud-paas-ta-org"></a>

#### 5.5.0 이하의 버전을 사용할 경우 해당 경로를 [https://nextcloud.paas-ta.org/~](https://nextcloud.paas-ta.org/~) 로 변경이 필요합니다. <a id="5-5-0-https-nextcloud-paas-ta-org"></a>

## PaaS-TA 5.5.0 가이드 문서 <a id="paas-ta-5-5-0"></a>

### 플랫폼 설치 가이드 <a id="undefined"></a>

* ​[설치 파일 다운로드 받기](https://paas-ta.kr/download/preVersion)​
* 운영 환경 설치
  * PaaS-TA 플랫폼 수동 설치
    * ​[BOSH 설치\(AWS, OpenStack\)](install-guide/bosh/bosh.md)​
    * ​[PaaS-TA 설치\(AWS, OpenStack\)](install-guide/paasta/paas-ta.md)​
    * ​[PaaS-TA-min 설치\(AWS\)](install-guide/paasta/paas-ta-min.md)​

### 가이드 <a id="undefined-1"></a>

* ​[CF Migration 가이드 \(3.1 to 4.0\)](paas-ta-migration.md)​

### 포털 설치 가이드 <a id="undefined-2"></a>

**VM Type 배포/ App Type 배포 중 배포 방식을 선택하여 설치한다.**

* BOSH를 이용한 VM Type 배포
  * ​[PaaS-TA 포털 UI](install-guide/portal/paas-ta-ui-vm-type.md)​
  * ​[PaaS-TA 포털 API](install-guide/portal/paas-ta-api-vm-type.md)​
* PaaS-TA 컨테이너에 App Type 배포
  * ​[PaaS-TA 포털](install-guide/portal/paas-ta-app-type.md)​

### Container-Platform 설치 가이드 <a id="container-platform"></a>

* 단독 배포
  * ​[단독 배포 설치](install-guide/portal-1/undefined.md)​
  * ​[단독 배포용 Release 설치](install-guide/portal-1/release.md)​
* Edge 배포
  * ​[Edge 배포 설치](install-guide/portal-1/edge.md)​
  * ​[Edge 배포용 Release 설치](install-guide/portal-1/edge-release.md)​
* CaaS 서비스 배포
  * ​[단독 배포 설치](install-guide/portal-1/caas.md)​
  * ​[CaaS 서비스용 Release 설치](install-guide/portal-1/caas-release.md)​

### 서비스 설치 가이드 <a id="undefined-3"></a>

**아래 서비스 설치 전에 BOSH, PaaS-TA, PaaS-TA 포털이 설치되어 있어야 한다.**

* DBMS 설치
  * ​[Cubrid](service-guide/dbms/cubrid.md)​
  * ​[MySQL](service-guide/dbms/mysql.md)​
* NOSQL 설치
  * ​[MongoDB](service-guide/nosql/mongodb.md)​
  * ​[Redis](service-guide/nosql/on-demand-redis.md)​
* Storage 설치
  * ​[GlusterFS](service-guide/storage/glusterfs.md)​
* MessageQueue 설치
  * ​[RabbitMQ](service-guide/messagequeue/rabbitmq.md)​
* Web IDE 설치
  * ​[Web IDE](service-guide/webide/web-ide.md)​
* Pinpoint APM 설치
  * ​[Pinpoint APM](service-guide/etc/pinpoint.md)
* 통합 개발 도구 설치
  * ​[배포파이프라인](service-guide/tools/undefined.md)​
  * ​[형상관리](service-guide/tools/source-control-service.md)​
  * ​[Logging 서비스](service-guide/tools/logging-service.md)​
  * ​[애플리케이션 Gateway 서비스](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/service-guide/tools/paas-ta_application_gateway_service_install_guide_v1.0)​
  * ​[라이프사이클 관리 서비스](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/service-guide/tools/paas-ta_lifecycle_management_service_install_guide_v1.0)​

### 모니터링 설치 가이드 <a id="undefined-4"></a>

* ​[PaaS-TA 모니터링](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/service-guide/monitoring/paas-ta_monitoring_install_guide_v5.0)​

### 활용 가이드 <a id="undefined-5"></a>

* ​[BOSH CLI V2\(Command Line Interface\) 사용](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/paas-ta_bosh_cli_v2_-_-v1.0)​
* ​[CF CLI\(Command Line Interface\) 사용](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/openpaas-cli)​
* ​[Eclipse plugin 개발도구 사용](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/open-paas)​
* ​[PaaS-TA 사용자 포털 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/portal/paas-ta_user_portal_use_guide_v1.1)​
* ​[PaaS-TA 운영자 포털 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/portal/paas-ta_admin_portal_use_guide_v1.1)​
* ​[PaaS-TA 배포 파이프라인 사용자 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/tools/paas-ta_delivery_pipeline_service_use_guide_v1.0)​
* ​[PaaS-TA 형상관리 서비스 사용자 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/tools/paas-ta_source_control_service_use_guide_v1.0)​
* ​[PaaS-TA Container 서비스 사용자 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/tools/paas-ta_container_service_use_guide_v2.0)​
* ​[PaaS-TA Logging 서비스 사용자 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/tools/paas-ta_logging_service_use_guide_v1.0)​
* ​[PaaS-TA Jenkins 서비스](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/use-guide/tools/paas-ta_jenkins_service_user_guide)​

### 개발 언어별 애플리케이션 가이드 <a id="undefined-6"></a>

* ​[Node.js](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_nodejs_develope_guide)​
* ​[PHP](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_php_develope_guide)​
* ​[Python](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_python_develope_guide)​
* ​[Ruby](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_ruby_develope_guide)​
* ​[Java](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_java_develope_guide)​
* ​[Go](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/sample-app-guide/openpaas_paasta_application_go_develope_guide)​

### 플랫폼 개발 가이드 <a id="undefined-7"></a>

* ​[스템셀 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/openpaas_paasta_build_stemcell_guide)​
* ​[서비스팩 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/servicepack_develope_guide)​
* ​[빌드팩 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/buildpack_develope_guide)​
* ​[애플리케이션 APIPlatform 도로주소 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/application_apiplatform_dorojuso_devlope_guide)​
* ​[퍼블릭 API 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/publicapi_devlope_guide)​
* ​[Java API 서비스 미터링 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/paas-ta_java_api_-_-_-_)​
* ​[Java 서비스 미터링 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/paas-ta_java_-_-_-_)​
* ​[Nodejs API 서비스 미터링 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/paas-ta_node.js_api_-_-_)​
* ​[On-Demand 서비스 개발 가이드](https://paastaguide.gitbook.io/paas-ta-5-5-0/guide-5.5.0-semini/development-guide/on_demand_deployment_guide)​

