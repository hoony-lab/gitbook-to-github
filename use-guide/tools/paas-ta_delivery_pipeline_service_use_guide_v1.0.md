# PAAS-TA\_DELIVERY\_PIPELINE\_SERVICE\_USE\_GUIDE\_V1.0

## \[PaaS-TA 배포 파이프라인 서비스 사용자 가이드\]

### 목차

1. [문서 개요](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#1)
   * [1.1. 목적](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#1-1)
   * [1.2. 범위](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#1-2)
2. [배포 파이프라인 서비스 신청](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#2)
   * [2.1. PaaS-TA 사용자 포탈 접속](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#2-1)
   * [2.2. 배포 파이프라인 서비스 신청](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#2-2)
   * [2.3. 배포 파이프라인 접속](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#2-3)
3. [배포 파이프라인 사용자 매뉴얼](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3)
   * [3.1. 배포 파이프라인 사용자 메뉴 구성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-1)
   * [3.2. 배포 파이프라인 사용자 메뉴 설명](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2)
   * [3.2.1. 사용자 관리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-1)
   * [3.2.1.1. 사용자 대시보드](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-1-1)
   * [3.2.1.1.1. 사용자 목록 검색 조회](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-1-1-1)
   * [3.2.1.1.2. 사용자 상세 조회/수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-1-1-2)
   * [3.2.2. 파이프라인](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2)
   * [3.2.2.1. 나의 파이프라인](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-1)
   * [3.2.2.2. 파이프라인 신규 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-2)
   * [3.2.2.2.1. 파이프라인 신규 생성\(1\)](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-2-1)
   * [3.2.2.2.2. 파이프라인 신규 생성\(2\)](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-2-2)
   * [3.2.2.2.3. 파이프라인 신규 생성\(3\)](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-2-3)
   * [3.2.2.3.   파이프라인 대시보드](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-3)
   * [3.2.2.3.1. 파이프라인 목록 검색 조회](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-3-1)
   * [3.2.2.3.2. 파이프라인 상세 정보 조회](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-3-2)
   * [3.2.2.3.3. 파이프라인 수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-3-3)
   * [3.2.2.3.4. 파이프라인 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-3-4)
   * [3.2.2.4.   파이프라인 상세](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4)
   * [3.2.2.4.1. 참여자 관리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-1)
   * [3.2.2.4.1.1. 참여자 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-1-1)
   * [3.2.2.4.1.2.    참여자 목록 검색 조회](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-1-2)
   * [3.2.2.4.1.3.    참여자 상세 정보 조회/수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-1-3)
   * [3.2.2.4.1.4.    참여자 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-1-4)
   * [3.2.2.4.2. 빌드 Job](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2)
   * [3.2.2.4.2.1.    빌드 Job 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-1)
   * [3.2.2.4.2.2.    빌드 Job 구성 조회/수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-2)
   * [3.2.2.4.2.3.    빌드 Job 실행](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-3)
   * [3.2.2.4.2.4.    빌드 Job 정지](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-4)
   * [3.2.2.4.2.5.    빌드 Job 로그/히스토리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-5)
   * [3.2.2.4.2.6.    빌드 Job 로그 다운로드](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-6)
   * [3.2.2.4.2.7.    빌드 Job 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-7)
   * [3.2.2.4.2.8.    빌드 Job 복제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-8)
   * [3.2.2.4.2.9.    빌드 Job 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-2-9)
   * [3.2.2.4.3. 테스트 Job](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3)
   * [3.2.2.4.3.1.    테스트 Job 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-1)
   * [3.2.2.4.3.2.    테스트 Job 구성 조회/수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-2)
   * [3.2.2.4.3.3.    테스트 Job 실행](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-3)
   * [3.2.2.4.3.4.    테스트 Job 정지](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-4)
   * [3.2.2.4.3.5.    테스트 Job 로그/히스토리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-5)
   * [3.2.2.4.3.6.    테스트 Job 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-6)
   * [3.2.2.4.3.7.    테스트 Job 품질 이슈 결과](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-7)
   * [3.2.2.4.3.8.    테스트 Job 복제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-8)
   * [3.2.2.4.3.9.    테스트 Job 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-3-9)
   * [3.2.2.4.4. 배포 Job](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4)
   * [3.2.2.4.4.1.    배포 Job 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-1)
   * [3.2.2.4.4.2.    배포 Job 구성 조회/수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-2)
   * [3.2.2.4.4.3.    배포 Job 실행](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-3)
   * [3.2.2.4.4.4.    배포 Job 정지](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-4)
   * [3.2.2.4.4.5.    배포 Job 로그/히스토리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-5)
   * [3.2.2.4.4.6.    배포 Job 현재 작업으로 롤백](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-6)
   * [3.2.2.4.4.7.    배포 Job 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-7)
   * [3.2.2.4.4.8.    배포 Job 복제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-8)
   * [3.2.2.4.4.9.    배포 Job 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-4-9)
   * [3.2.2.4.5. Job 작업 정렬](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-5)
   * [3.2.2.4.6. 새 작업 그룹 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-2-4-6)
   * [3.2.3.     파이프라인 관리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-3)
   * [3.2.3.1.   Cloud Foundry 정보 관리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-3-1)
   * [3.2.3.1.1. Cloud Foundry 계정 등록](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-3-1-1)
   * [3.2.3.1.2. Cloud Foundry 계정 수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-3-1-2)
   * [3.2.4.     품질 관리](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4)
   * [3.2.4.1.   품질 이슈](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-1)
   * [3.2.4.2.   코딩 규칙](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-2)
   * [3.2.4.3.   품질 프로파일](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3)
   * [3.2.4.3.1. 품질 프로파일 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3-1)
   * [3.2.4.3.2. 품질 프로파일 복제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3-2)
   * [3.2.4.3.3. 품질 프로파일 수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3-3)
   * [3.2.4.3.4. 품질 프로파일 프로젝트 연결](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3-4)
   * [3.2.4.3.5. 품질 프로파일 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-3-5)
   * [3.2.4.4.   품질 게이트](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4)
   * [3.2.4.4.1. 품질 게이트 생성](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-1)
   * [3.2.4.4.2. 품질 게이트 복제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-2)
   * [3.2.4.4.3. 품질 게이트 수정](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-3)
   * [3.2.4.4.4. 품질 게이트 조건 추가](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-4)
   * [3.2.4.4.5. 품질 게이트 프로젝트 연결](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-5)
   * [3.2.4.4.6. 품질 게이트 삭제](paas-ta_delivery_pipeline_service_use_guide_v1.0.md#3-2-4-4-6)

## 1. 문서 개요

### 1.1 목적

본 문서는 배포 파이프라인 서비스를 사용할 사용자의 사용 방법에 대해 기술하였다.

### 1.2 범위

본 문서는 Windows 환경을 기준으로 배포 파이프라인 서비스를 사용할 사용자의 사용 방법에 대해 작성되었다.

## 2. 배포 파이프라인 서비스 신청

### 2.1 PaaS-TA 사용자 포탈 접속

1. PaaS-Ta 사용자 포탈에 접속하여 “로그인”을 클릭한다. ![](../../.gitbook/assets/image002%20%284%29.png)
2. 사용자 계정과 비밀번호를 입력한 후 “LOGIN” 버튼을 클릭하여 사용자 포탈에 로그인한다. ![](../../.gitbook/assets/image003.jpg)
3. 로그인 한 후 카탈로그 페이지로 이동한다. ![](../../.gitbook/assets/image004.jpg)

### 2.2 배포 파이프라인 서비스 신청

1. 배포 파이프라인 서비스를 선택한다. ![](../../.gitbook/assets/image005%20%284%29.png)
2. 신청하고자 하는 공간을 선택한다. 이미 배포 파이프라인을 사용중인 ORG에서는 아래 첨부 사진과 같이 에러가 출력된다. ![](../../.gitbook/assets/image006.jpg)
3. 서비스 이름을 입력한다. 이미 신청한 배포 파이프라인의 이름이 있을 경우 아래 첨부 사진과 같이 에러가 출력된다. ![](../../.gitbook/assets/image007.jpg)
4. 신청을 진행한 후 스페이스 페이지의 서비스 탭에서 파이프라인 서비스 생성을 확인한다. ![](../../.gitbook/assets/image008%20%281%29.png)

### 2.3 배포 파이프라인 접속

1. PaaS-TA 포탈의 스페이스 페이지에서 신청된 배포 파이프라인의 “대시보드” 버튼을 클릭하여 접속을 진행한다. ![](../../.gitbook/assets/image009%20%281%29.png)
2. 배포 파이프라인 접속을 확인한다. ![](../../.gitbook/assets/image010.jpg)
3. 이 때 _**배포 파이프라인 서비스를 처음 생성한 사용자가 관리자가 된다.**_ 우측 상단의 사용자 관리 메뉴를 클릭하여 사용자 대시보드로 이동한다.

_**※ 처음 접속하는 사용자는 관리자의 승인이 없을 경우 접속할 수 없습니다.**_

## 3. 배포 파이프라인 사용자 매뉴얼

본 장에서는 배포 파이프라인의 메뉴 구성 및 화면 설명에 대해서 기술하였다.

### 3.1 배포 파이프라인 사용자 메뉴 구성

배포 파이프라인 서비스는 파이프라인을 조회 및 관리하는 부분과 Cloud Foundry 계정 정보를 관리하는 부분, 소스 코드의 품질을 관리하는 부분, 부여된 권한에 따라 사용자를 관리할 수 있는 사용자 관리 부분으로 구성되어 있다.

_**※ 기본적으로 사용자는 사용자 관리, 파이프라인 관리, 품질 관리 메뉴가 보이지 않는다.**_

| 메뉴 | 분류 | 설명 |
| :--- | :--- | :--- |
| 사용자 관리 | 사용자 대시보드 | 파이프라인을 사용하는 사용자들의 정보 조회 및 관리 |
| 파이프라인 | 파이프라인 대시보드 | 파이프라인 등록, 목록 및 상세 조회, 삭제, 참여자 권한 등의 기능을 관리 |
| 파이프라인 관리 | Cloud Foundry 정보 관리 | Cloud Foundry 계정 정보를 관리 |
| 품질 관리 | 품질 이슈 | 수행된 소스 코드의 오류 해결 여부 및 오류의 수준, 활성화 상태를 관리 |
| 코딩 규칙 | 표준화된 소스 코드의 코딩 양식을 관리 |  |
| 품질 프로파일 | 소스 코드의 품질을 분석하기 위한 기준이 되는 코딩 규칙을 관리 |  |
| 품질 게이트 | 소스 코드의 품질 분석 결과 정해진 품질 기준 값을 초과하거나 미달하지 않았는지에 대한 기준을 관리 |  |

### 3.2 배포 파이프라인 사용자 메뉴 설명

본 장에서는 배포 파이프라인의 4개 메뉴에 대한 설명을 기술한다.

#### 3.2.1. 사용자 관리

사용자 관리 메뉴는 관리자에게만 보이는 메뉴로써 본 장에서는 배포 파이프라인 서비스를 사용하는 사용자들의 권한 관리 및 정보 조회와 관리에 대한 설명을 기술한다.

**3.2.1.1. 사용자 대시보드**

**3.2.1.1.1. 사용자 목록 검색 조회**

1. 상단의 우측 메뉴에 “사용자 관리” 버튼을 클릭한다. ![](../../.gitbook/assets/image011%20%281%29.png)
2. 사용자 대시보드로 이동한다. ![](../../.gitbook/assets/image012.jpg)
3. 사용자 대시보드 목록에서 검색할 수 있는 조건에는 사용자 아이디를 검색어로 하는 검색과 검색 타입이 관리자/사용자인 검색 2개가 있다. 검색어를 입력 후 “ENTER”를 하거나 “돋보기” 버튼을 클릭한다. ![](../../.gitbook/assets/image013%20%281%29.png) ![](../../.gitbook/assets/image014%20%281%29.png)

**3.2.1.1.2. 사용자 상세 조회/수정**

1. 사용자 대시보드에서 아이디를 선택하여 사용자 상세 조회/수정 페이지로 이동한다. ![](../../.gitbook/assets/image015%20%281%29.png)
2. 사용자의 정보를 상세 조회한다. ![](../../.gitbook/assets/image016.jpg)
3. 정보를 수정하고자 할 때 입력 값을 새롭게 입력한다. 권한은 관리자/사용자로 선택할 수 있다. ![](../../.gitbook/assets/image017.jpg)
4. 입력 값을 수정 후 “수정” 버튼을 클릭한다. ![](../../.gitbook/assets/image018%20%281%29.png)
5. 수정된 정보를 확인한다. ![](../../.gitbook/assets/image019.jpg)

#### 3.2.2. 파이프라인

본 장에서는 파이프라인을 관리하는 2개 방법에 대한 설명을 기술한다.

_**※ 기본적으로 사용자는 파이프라인을 보기만 할 수 있고, 생성, 조회, 수정, 삭제할 수 없다. 하지만 파이프라인 참여자 권한이 부여되었을 경우에는 조건에 따라 파이프라인 생성, 조회, 수정, 삭제가 가능해진다.**_

**3.2.2.1. 나의 파이프라인**

1. 어떤 페이지에 있든 상관없이 Drop down 메뉴의 나의 파이프라인 버튼을 클릭하면 대시보드로 이동한다.

   ![](../../.gitbook/assets/image020%20%281%29.png)

   ![](../../.gitbook/assets/image021.jpg)

2. 나의 파이프라인에서 목록의 파이프라인 명을 클릭하면 해당 파이프라인의 상세 페이지로 이동한다.

   ![](../../.gitbook/assets/image022%20%281%29.png)

   ![](../../.gitbook/assets/image023%20%281%29.png)

**3.2.2.2. 파이프라인 신규 생성**

**3.2.2.2.1. 파이프라인 신규 생성\(1\)**

1. 상단 메뉴의 “파이프라인 목록” 을 클릭하면 파이프라인 명 입력만으로 즉시 파이프라인을 신규 생성할 수 있다.

   ![](../../.gitbook/assets/image024%20%281%29.png)

2. 등록 버튼을 누른 후 대시보드에 추가된 파이프라인을 확인할 수 있다.

   ![](../../.gitbook/assets/image025%20%281%29.png)

3. Drop down 메뉴에도 등록된 파이프라인을 확인할 수 있다.

   ![](../../.gitbook/assets/image026%20%285%29.png)

   **3.2.2.2.2. 파이프라인 신규 생성\(2\)**

4. 오른쪽 상단의 “신규 생성” 버튼을 클릭한다.

   _**※ 사용자는 “신규 생성” 버튼이 활성화되지 않는다.**_

   ![](../../.gitbook/assets/image027%20%284%29.png)

5. 파이프라인 신규 생성 페이지로 이동한다.

   ![](../../.gitbook/assets/image028%20%281%29.png)

6. 파이프라인 명은 필수로 입력받기 때문에 파이프라인 명을 반드시 입력한 후 “생성” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image029%20%281%29.png)

7. 생성된 후 파이프라인 대시보드에서 새로 추가된 파이프라인 명을 확인한다.

   ![](../../.gitbook/assets/image030%20%281%29.png)

**3.2.2.2.3. 파이프라인 신규 생성\(3\)**

_**※ 사용자는 파이프라인에 대한 참여자 권한이 주어지지 않으면 파이프라인 상세 페이지로 이동할 수 없다.**_ 1. 오른쪽 상단의 “신규 생성” 버튼을 클릭한다. ![](../../.gitbook/assets/image031%20%281%29.png) 2. 파이프라인 신규 생성 페이지로 이동한다. ![](../../.gitbook/assets/image032.jpg) 3. 이후 생성 과정은 3.2.2.2.2. 파이프라인 신규 생성\(2\) 참고.

**3.2.2.3. 파이프라인 대시보드**

**3.2.2.3.1. 파이프라인 목록 검색 조회**

1. 목록에서 검색할 수 있는 조건은 파이프라인 명과 날짜/업데이트/이름순 필터 2개이다.

   ![](../../.gitbook/assets/image033%20%281%29.png)

2. 검색어를 입력 후 “ENTER”를 하거나 “돋보기” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image034%20%281%29.png)

3. 목록 조회/검색 목록 조회일 때 모두 검색 타입 필터를 선택하면 즉시 반영된다.

   ![](../../.gitbook/assets/image035%20%281%29.png)

**3.2.2.3.2. 파이프라인 상세 정보 조회**

1. 파이프라인 대시보드에서 파이프라인 명을 눌러 파이프라인 상세 페이지로 이동한다.

   ![](../../.gitbook/assets/image036.jpg)

2. 파이프라인 상세페이지에서 오른쪽 상단에 “정보보기/수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image037%20%281%29.png)

3. 파이프라인 정보보기/수정 페이지로 이동하여 해당 파이프라인의 정보를 상세 조회한다.

   ![](../../.gitbook/assets/image038%20%281%29.png)

**3.2.2.3.3. 파이프라인 수정**

1. 파이프라인 정보보기/수정 페이지에서 입력 값을 수정 후 “수정” 버튼을 클릭한다. ![](../../.gitbook/assets/image039.jpg)
2. 수정하고 나면 파이프라인 상세 페이지로 이동한다. 다시 “정보보기/수정” 버튼을 클릭하여 변경된 값을 확인한다. ![](../../.gitbook/assets/image040.jpg)

**3.2.2.3.4. 파이프라인 삭제**

1. 파이프라인 정보보기/수정 페이지에서 “파이프라인 삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image041%20%284%29.png)

2. 삭제 후에 파이프라인 대시보드로 이동하여 파이프라인이 삭제되었는지 확인한다.

   ![](../../.gitbook/assets/image042.jpg)

**3.2.2.4. 파이프라인 상세**

본 장에서는 파이프라인에 참여하는 참여자 추가, 수정, 삭제 및 권한 부여 등 전반적인 참여자 관리와 하나의 파이프라인에 대한 Job들을 생성 및 실행, 삭제, 로그/히스토리 조회, 참여자 등을 관리하는 방법에 대해 기술한다.

**3.2.2.4.1. 참여자 관리**

**3.2.2.4.1.1. 참여자 추가**

_**※ 관리자는 파이프라인 생성자이므로 기본적으로 생성 권한을 가진 참여자이다.**_ 1. 파이프라인 상세페이지에서 참여자 탭을 선택한다. ![](../../.gitbook/assets/image043%20%281%29.png) 2. 우측 상단에 “참여자 추가” 버튼을 클릭한다. ![](../../.gitbook/assets/image044%20%281%29.png) 3. 참여자 추가 페이지로 이동하여 현재 배포 파이프라인에 초대된 사용자를 검색하고, 선택한다. 권한은 보기, 생성, 실행 권한 중 한 개를 선택하여 “추가” 버튼을 클릭한다. ![](../../.gitbook/assets/image045%20%281%29.png) 4. 참여자 탭에서 추가된 참여자를 확인한다. ![](../../.gitbook/assets/image046%20%281%29.png)

**3.2.2.4.1.2. 참여자 목록 검색 조회**

1. 참여자 목록에서 검색할 수 있는 조건은 참여자 아이디 검색 1개이다.

   ![](../../.gitbook/assets/image047%20%281%29.png)

2. 검색어를 입력 후 “ENTER”를 하거나 “돋보기” 버튼을 클릭한 후 검색 조회를 한다.

   ![](../../.gitbook/assets/image048.jpg)

**3.2.2.4.1.3.    참여자 상세 정보 조회/수정**

1. 참여자 목록에서 정보 조회할 참여자 아이디를 선택한다.

   ![](../../.gitbook/assets/image049%20%281%29.png)

2. 참여자 상세 정보 조회/수정 페이지로 이동하여 참여자의 상세 정보를 조회한다.

   ![](../../.gitbook/assets/image050.jpg)

3. 수정하고자 하는 권한을 선택하여 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image051%20%281%29.png)

4. 참여자 목록에서 수정된 참여자의 권한을 확인한다.

   ![](../../.gitbook/assets/image052%20%281%29.png)

**3.2.2.4.1.4.    참여자 삭제**

1. 참여자 목록에서 삭제할 참여자 아이디를 선택한다.

   ![](../../.gitbook/assets/image053%20%281%29.png)

2. 참여자 상세 정보 조회/수정 페이지로 이동한다.

   ![](../../.gitbook/assets/image053%282%29.png)

3. “참여자 삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image054%20%281%29.png)

4. 참여자 목록에서 참여자가 삭제되었음을 확인한다.

   ![](../../.gitbook/assets/image055.jpg)

**3.2.2.4.2. 빌드 Job**

**3.2.2.4.2.1. 빌드 Job 생성**

1. 파이프라인 상세 페이지에서 ‘이곳을 클릭하여 새 작업 추가’ 버튼을 클릭한다.

   ![](../../.gitbook/assets/image056%20%281%29.png)

2. 구성 상세페이지로 이동한다.

   ![](../../.gitbook/assets/image057.jpg)

3. 작업 유형을 선택한 후 원하는 저장소를 입력 유형으로 선택한다. 그 후에 선택한 저장소의 아이디와 비밀번호를 입력 후 해당하는 저장소의 경로를 입력하여 “조회” 버튼을 클릭한다. 조회한 Branch를 선택 후 작업 트리거는 각자의 상황에 맞게 선택한다.  ![](../../.gitbook/assets/image058.jpg)
4. 생성된 빌드 Job 을 파이프라인 상세 페이지에서 확인한다. ![](../../.gitbook/assets/image059%20%281%29.png)

_**※ 빌드 Job 생성은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 생성이 가능하다.**_

**3.2.2.4.2.2. 빌드 Job 구성 조회/수정**

1. 생성된 빌드 Job 의 “구성” 아이콘을 클릭한다.  


   ![](../../.gitbook/assets/image060.png)

2. 구성 상세페이지로 이동하여 생성 시 저장해 놓았던 구성 정보들을 조회한다.

   ![](../../.gitbook/assets/image061.jpg)

3. 수정 시에는 각 입력 폼에 수정할 정보들을 다시 입력한 후 “저장” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image062.png)

4. 구성 상세페이지로 이동하여 수정된 정보들을 확인한다.

   ![](../../.gitbook/assets/image063.jpg)

_**※ 빌드 Job 구성 조회는 파이프라인 참여자이면 모두 조회가 가능하다. 하지만 수정은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 수정이 가능하다.**_

**3.2.2.4.2.3. 빌드 Job 실행**

1. 파이프라인 상세페이지에서 “실행” 아이콘을 클릭한다.  


   ![](../../.gitbook/assets/image064.png)

2. 실행이 될 때 파란색으로 바뀌며 깜빡이는 것을 확인할 수 있다. \(실행 중에 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.\)

   ![](../../.gitbook/assets/image065.jpg)

3. 실행이 완료되면 초록색으로 바뀌며 작업 부분에 Build\(실행완료\) 로 표시된다.

   ![](../../.gitbook/assets/image066.jpg)

_**※ 빌드 Job 실행은 관리자와 파이프라인 참여자 중 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.2.4. 빌드 Job 정지**

1. 실행 중인 빌드 Job을 정지 및 취소하고 싶을 때 “정지” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image067.png)

2. 정지된 빌드 Job은 주황색으로 바뀌는 것을 확인할 수 있다.

   ![](../../.gitbook/assets/image068.jpg)

_**※ 빌드 Job 정지는 관리자와 파이프라인 참여자 중 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.2.5.    빌드 Job 로그/히스토리**

1. 빌드 Job 실행이 진행 중일 때 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.

   ![](../../.gitbook/assets/image069.png)

2. 로그 조회 페이지로 이동한다. 실시간으로 로그가 보이고 있는 것을 확인한다.

   ![](../../.gitbook/assets/image070.jpg)

3. 빌드 Job 실행이 완료된 것을 확인하고, 히스토리를 확인한다.

   ![](../../.gitbook/assets/image071.jpg)

4. 로그/히스토리 페이지에서 상단의 “취소” 버튼 또는 “실행” 버튼을 클릭하여 Job을 취소하고, 실행할 수 있다.

   ![](../../.gitbook/assets/image072.png)

   ![](../../.gitbook/assets/image073.png)

5. 로그/히스토리 페이지에서 “구성” 버튼을 클릭하면 빌드 Job 구성 조회 페이지로 이동한다.

   ![](../../.gitbook/assets/image074.png)

6. 로그/히스토리 페이지에서 “목록” 버튼을 클릭하면 파이프라인 상세페이지로 이동한다.

   ![](../../.gitbook/assets/image075.png)

_**※ 빌드 Job 로그/히스토리는 관리자와 모든 파이프라인 참여자가 조회 가능하나 실행 및 정지 버튼은 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.2.6.    빌드 Job 로그 다운로드**

1. 빌드 Job 실행이 성공하면 로그/히스토리 페이지에서 “다운로드” 버튼이 활성화된다. 활성화된 “다운로드” 버튼을 클릭한다.  


   ![](../../.gitbook/assets/image076.png)

2. Build 된 파일\(ex. war, jar, zip 등\)이 다운로드 된다.

   ![](../../.gitbook/assets/image077.png)

_**※ 빌드 Job 로그 다운로드는 관리자와 모든 파이프라인 참여자가 가능하다.**_

**3.2.2.4.2.7.    빌드 Job 추가**

1. 파이프라인 상세페이지에서 빌드 Job의 “추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image078.png)

2. 구성 상세페이지로 이동한다.

   ![](../../.gitbook/assets/image079.jpg)

3. 빌드 Job 생성과 같이 입력 폼에 알맞은 값들을 입력한 후 ”저장” 버튼을 클릭한다.\(작업 유형은 빌드 이외에도 테스트, 배포 유형 선택이 가능하다.\)

   ![](../../.gitbook/assets/image080.jpg)

4. 추가된 빌드 Job 을 확인한다.  


   ![](../../.gitbook/assets/image081.png)

5. 작업 트리거에서 이 작업\(Job\)을 새 작업 그룹으로 구성을 체크하면 새로운 그룹으로 Job이 추가된다. \(이후에 새 작업 그룹 추가 항목에서 설명하겠습니다.\)

_**※ 빌드 Job 추가는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.2.8.    빌드 Job 복제**

1. 파이프라인 상세페이지에서 빌드 Job의 “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image082.png)

2. 빌드 Job 이 복제된 것을 확인한다.

   ![](../../.gitbook/assets/image083.jpg)

_**※ 빌드 Job 복제는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.2.9.    빌드 Job 삭제**

1. 파이프라인 상세페이지에서 빌드 Job의 “삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image084.png)

2. 빌드 Job 이 삭제된 것을 확인한다.

   ![](../../.gitbook/assets/image084%282%29.png)

_**※ 빌드 Job 삭제는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.3. 테스트 Job**

**3.2.2.4.3.1. 테스트 Job 생성**

1. Job의 “추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image084%283%29.png)

2. 구성 페이지로 이동하여 작업 유형을 테스트\(Test\)로 선택한 후 입력 유형에서 원하는 품질 프로파일과 품질 게이트, 작업 그룹을 선택한다. 그 후에 작업 트리거는 각자의 상황에 맞게 선택한다.

   ![](../../.gitbook/assets/image085.jpg)

3. “저장” 버튼을 클릭하고, 파이프라인 상세페이지에서 테스트 Job 생성된 것을 확인한다.

   ![](../../.gitbook/assets/image086.png)

_**※ 테스트 Job 생성은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 생성이 가능하다.**_

**3.2.2.4.3.2. 테스트 Job 구성 조회/수정**

1. 생성된 테스트 Job 의 “구성” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image087.png)

2. 구성 상세페이지로 이동하여 생성 시 저장해 놓았던 구성 정보들을 조회한다.

   ![](../../.gitbook/assets/image087%282%29.png)

3. 수정 시에는 각 입력 폼에 수정할 정보들을 다시 입력한 후 “저장” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image088.png)

4. 구성 상세페이지로 이동하여 수정된 정보들을 확인한다.

   ![](../../.gitbook/assets/image089.jpg)

_**※ 테스트 Job 구성 조회는 파이프라인 참여자이면 모두 조회가 가능하다. 하지만 수정은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 수정이 가능하다.**_

**3.2.2.4.3.3.    테스트 Job 실행**

1. 파이프라인 상세페이지에서 테스트 Job의 “실행” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image090.png)

2. 실행이 될 때 파란색으로 바뀌며 깜빡이는 것을 확인할 수 있다. \(실행 중에 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.\)

   ![](../../.gitbook/assets/image091.jpg)

3. 실행이 완료되면 초록색으로 바뀌며 작업 부분에 Test\(실행완료\) 로 표시된다.

_**※ 테스트 Job 실행은 관리자와 파이프라인 참여자 중 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.3.4.    테스트 Job 정지**

1. 실행 중인 테스트 Job을 정지 및 취소하고 싶을 때 “정지” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image092.png)

2. 정지된 빌드 Job은 주황색으로 바뀌는 것을 확인할 수 있다

   ![](../../.gitbook/assets/image093.jpg)

_**※ 테스트 Job 정지는 관리자와 파이프라인 참여자 중 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.3.5.    테스트 Job 로그/히스토리**

1. 빌드 Job 실행이 진행 중일 때 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.

   ![](../../.gitbook/assets/image094.png)

2. 로그 조회 페이지로 이동한다. 실시간으로 로그가 보이고 있는 것을 확인한다.

   ![](../../.gitbook/assets/image095.jpg)

3. 테스트 Job 실행이 완료된 것을 확인하고, 히스토리를 확인한다.

   ![](../../.gitbook/assets/image096.jpg)

4. “실행”, “취소”, “구성”, “목록” 버튼은 3.2.2.4.2.5. 빌드 Job 로그/히스토리 항목을 참고한다.

_**※ 테스트 Job 로그/히스토리는 관리자와 모든 파이프라인 참여자가 조회 가능하나 실행 및 정지 버튼은 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.3.6.    테스트 Job 품질 이슈 결과**

1. 테스트 Job의 로그/히스토리 “품질 이슈 결과” 버튼을 누르면 수행된 소스 코드의 오류 해결 여부 및 오류의 수준, 활성화 상태를 관리하는 품질 관리 대시보드로 이동한다.

   ![](../../.gitbook/assets/image097.jpg)

2. 품질 관리 대시보드에 대해서는 이후 3.2.4 품질 관리 부분을 참고한다.

_**※ 테스트 Job 품질 이슈 결과는 관리자와 모든 파이프라인 참여자가 조회 가능하다.**_

**3.2.2.4.3.7.    테스트 Job 추가**

1. 파이프라인 상세페이지에서 테스트 Job의 “추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image098.png)

2. 그 이후의 과정은 3.2.2.4.2.7. 빌드 Job 추가 항목을 참고한다.

_**※ 테스트 Job 추가는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.3.8.    테스트 Job 복제**

1. 파이프라인 상세페이지에서 테스트 Job의 “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image099.png)

2. 테스트 Job 이 복제된 것을 확인한다.

   ![](../../.gitbook/assets/image100.jpg)

_**※ 테스트 Job 복제는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.3.9.    테스트 Job 삭제**

1. 파이프라인 상세페이지에서 테스트 Job의 “삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image101.png)

2. 테스트 Job 이 삭제된 것을 확인한다.

   ![](../../.gitbook/assets/image102.jpg)

_**※ 테스트 Job 삭제는 관리자와 생성 권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.4.    배포 Job**

**3.2.2.4.4.1.    배포 Job 생성**

1. Job의 “추가” 버튼을 클릭한다.  


   ![](../../.gitbook/assets/image103.png)

2. 구성 페이지로 이동하여 작업 유형을 배포\(Deploy\)로 선택한 후 유형에서 원하는 배포 유형을 선택, 파이프라인 관리에서 저장해 놓은 Cloud Foundry 정보를 선택한다. \(Cloud Foundry 정보를 가져오기 위해서는 선행 과정이 필요하다. 과정은 3.2.3.1. Cloud Foundry 정보 관리 항목을 참고한다\). 그다음 MANIFEST 사용 여부를 체크 후 입력 유형과 작업 트리거를 차례로 입력한다.

   ![](../../.gitbook/assets/image104%20%281%29%20%281%29.jpg)

3. “저장” 버튼을 클릭하고, 파이프라인 상세페이지에서 배포 Job이 생성된 것을 확인한다.

   ![](../../.gitbook/assets/image105.png)

_**※ 배포 Job 생성은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 생성이 가능하다.**_

**3.2.2.4.4.2.    배포 Job 구성 조회/수정**

1. 생성된 배포 Job 의 “구성” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image106.png)

2. 구성 상세페이지로 이동하여 생성 시 저장해 놓았던 구성 정보들을 조회한다.

   ![](../../.gitbook/assets/image104%20%281%29.jpg)

3. 수정 시에는 각 입력 폼에 수정할 정보들을 다시 입력한 후 “저장” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image107.png)

4. 구성 상세페이지로 이동하여 수정된 정보들을 확인한다.

_**※ 배포 Job 구성 조회는 파이프라인 참여자이면 모두 조회가 가능하다. 하지만 수정은 관리자와 파이프라인 참여자 중 생성 권한을 가진 참여자만 수정이 가능하다.**_

**3.2.2.4.4.3.    배포 Job 실행**

1. 파이프라인 상세페이지에서 배포 Job의 “실행” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image108.png)

2. 실행이 될 때 파란색으로 바뀌며 깜빡이는 것을 확인할 수 있다. \(실행 중에 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.\)

   ![](../../.gitbook/assets/image109.jpg)

3. 실행이 완료되면 초록색으로 바뀌며 작업 부분에 Deploy\(실행완료\) 로 표시된다.

   ![](../../.gitbook/assets/image110.jpg)

_**※ 배포 Job 실행은 관리자와 파이프라인 참여자 중 생성 권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.4.4.    배포 Job 정지**

1. 실행 중인 배포 Job을 정지 및 취소하고 싶을 때 “정지” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image111.png)

2. 정지된 배포 Job이 주황색으로 바뀌는 것을 확인할 수 있다.

   ![](../../.gitbook/assets/image112.jpg)

_**※ 배포 Job 정지는 관리자와 파이프라인 참여자 중 생성권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.4.5.    배포 Job 로그/히스토리**

1. 배포 Job 실행이 진행 중일 때 “로그/히스토리” 아이콘을 클릭하여 실시간으로 로그를 조회할 수 있다.

   ![](../../.gitbook/assets/image113.png)

2. 로그 조회 페이지로 이동한다. 실시간으로 로그가 보이고 있는 것을 확인한다.

   ![](../../.gitbook/assets/image114.jpg)

3. 배포 Job 실행이 완료된 것을 확인하고, 히스토리를 확인한다.

   ![](../../.gitbook/assets/image115.jpg)

4. PaaS-TA 포탈로 Cloud Foundry 계정을 만들어 배포한 결과 PaaS-TA 포탈 대시보드에서 공간의 애플리케이션 부분에 ‘testtest’라는 애플리케이션이 배포되었음을 확인할 수 있다.

   ![](../../.gitbook/assets/image116.png)

_**※ 배포 Job 로그/히스토리는 관리자와 모든 파이프라인 참여자가 조회 가능하나 실행 및 정지 버튼은 생성권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.4.6.    배포 Job 현재 작업으로 롤백**

1. 배포 Job 의 로그/히스토리 페이지에서 “현재 작업으로 롤백” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image117.png)

2. 현재 작업으로 롤백하는 창이 뜨고 Cloud Foundry 정보와 조직/공간 입력 값을 수정할 수 있다.

   예를 들어 애플리케이션 명을 ‘test-hrjin’이라 수정하고 “롤백” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image118.png)

3. 롤백을 진행한다.

   ![](../../.gitbook/assets/image119.jpg)

4. 롤백 진행 후 PaaS-TA 포탈에서 확인한 결과 ‘test-hrjin’ 이란 이름의 애플리케이션이 배포 완료되었다.

   ![](../../.gitbook/assets/image120.png)

_**※ 배포 Job 현재 작업으로 롤백은 관리자와 생성권한과 실행 권한을 가진 참여자만 가능하다.**_

**3.2.2.4.4.7.    배포 Job 추가**

1. 파이프라인 상세페이지에서 테스트 Job의 “추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image121.png)

2. 그 이후의 과정은 3.2.2.4.2.7. 빌드 Job 추가, 3.2.2.4.3.7. 테스트 Job 추가 항목을 참고한다.

_**※ 배포 Job 추가는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.4.8.    배포 Job 복제**

1. 파이프라인 상세페이지에서 배포 Job의 “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image122.png)

2. 배포 Job 이 복제된 것을 확인한다.

   ![](../../.gitbook/assets/image123.jpg)

_**※ 배포 Job 복제는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.4.9.    배포 Job 삭제**

1. 파이프라인 상세페이지에서 배포 Job의 “삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image124.png)

2. 배포 Job 이 삭제된 것을 확인한다.

   ![](../../.gitbook/assets/image125.jpg)

_**※ 배포 Job 삭제는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.5. Job 작업 정렬**

1. 파이프라인 상세페이지에서 각 Job의 “작업 정렬” 아이콘을 클릭한다.

   ![](../../.gitbook/assets/image126.png)

2. 현재 작업 그룹 내에서 정렬할 수 있는 나머지 Job의 번호의 목록이 drop down 메뉴로 보인다.

   ![](../../.gitbook/assets/image127.png)

3. 그중 정렬하고자 하는 번호를 클릭 시 그 번호의 Job 과 위치가 서로 바뀌게 되며 정렬된다.

   ![](../../.gitbook/assets/image128.jpg)

_**※ Job 작업 정렬은 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.**_

**3.2.2.4.6. 새 작업 그룹 추가**

1. 파이프라인 상세페이지에서 “새 작업 그룹 추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image129.png)

2. Job 구성 상세페이지로 이동한다.

   ![](../../.gitbook/assets/image130.jpg)

3. 새로운 Job 한 개를 생성한 뒤 파이프라인 상세페이지로 이동한다. 이전 작업 그룹 아래로 점선이 생기며 새로 생성한 Job이 새로운 그룹 내로 나눠진 것을 확인할 수 있다.

   ![](../../.gitbook/assets/image131.png)

_**※ Job 새 작업 그룹 추가는 관리자와 생성권한을 가진 파이프라인 참여자만 가능하다.**_

#### 3.2.3. 파이프라인 관리

본 장에서는 Cloud Foundry 정보를 등록하여 Job을 배포할 Cloud Foundry target URL을 연동하는 과정에 대하여 기술한다.

**3.2.3.1. Cloud Foundry 정보 관리**

**3.2.3.1.1. Cloud Foundry 계정등록**

1. Cloud Foundry 정보 관리 대시보드에서 우측 상단에 “Cloud Foundry 계정 등록” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image132.png)

2. Cloud Foundry 계정 등록 페이지로 이동한다.

   ![](../../.gitbook/assets/image133.jpg)

3. 계정 명을 입력한 후 아이디와 비밀번호에는 Cloud Foundry 로그인에 필요한 아이디와 비밀번호를 입력한다.

   ![](../../.gitbook/assets/image134.png)

4. URL에는 “URL 관리” 버튼을 클릭한 후 팝업 창이 뜨면 “URL 등록” 버튼을 클릭하여 Cloud Foundry 계정 정보를 등록하도록 한다.  


   ![](../../.gitbook/assets/image135.png)

5. Cloud Foundry API 명\(ex. api\)을 입력하고, Job 배포 계정으로 사용하고자 하는 Cloud Foundry target API URL\(ex. [https://api.115.68.46.186.xip.io](https://api.115.68.46.186.xip.io)\) 을 입력한 후 “URL 저장” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image136.png)

6. URL이 등록되었음을 확인한다.  


   ![](../../.gitbook/assets/image137.png)

7. 다시 계정 등록 화면으로 돌아와 등록한 URL을 선택 후 나머지 값을 입력한다. 마지막으로 “등록” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image138.png)

8. Cloud Foundry 정보 관리 대시보드에서 Cloud Foundry 계정 정보가 정상적으로 등록되었음을 확인한다.

   ![](../../.gitbook/assets/image139.png)

**3.2.3.1.2. Cloud Foundry 계정수정**

1. Cloud Foundry 정보 관리 대시보드에서 수정하고자 하는 계정을 클릭한다.

   ![](../../.gitbook/assets/image140.jpg)

2. 수정할 값들을 입력하고 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image141.png)

3. 다시 대시보드를 통해 계정 상세 페이지로 이동 후 수정되었는지 확인한다.

#### 3.2.4. 품질 관리

본 장에서는 테스트 Job을 통해 검사한 소스 코드와 관련하여 품질 이슈와 코딩 규칙, 품질 프로파일, 품질 게이트에 대한 설명을 기술한다.

**3.2.4.1. 품질 이슈**

1. 품질 관리 메뉴에서 품질 이슈를 클릭하여 품질 이슈 대시보드로 이동한다.

   ![](../../.gitbook/assets/image142.jpg)

2. “규칙상세” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image143.jpg)

3. 오른쪽 상단의 “목록” 버튼을 클릭하면 Job 테스트 목록이 있는 품질 이슈 대시보드로 이동한다.

**3.2.4.2. 코딩 규칙**

1. 품질 관리 메뉴에서 코딩 규칙을 클릭하여 코딩 규칙 대시보드로 이동한다.

   ![](../../.gitbook/assets/image144.jpg)

2. “프로파일에 추가” 버튼을 클릭한 뒤 프로파일 추가 팝업 창에서 이슈 수준을 정한 후 “추가” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image145.png)

3. 왼쪽 하단에 품질 프로파일 메뉴에서 이전 단계에서 선택한 Default^Default-QualityProfle 을 클릭하여 추가한 코딩 규칙이 추가되었는지 확인한다.  


   ![](../../.gitbook/assets/image146.png)

4. “프로파일에 제거” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image147.png)

5. 제거한 코딩 규칙이 품질 프로파일에서 삭제된 것을 확인한다.

   ![](../../.gitbook/assets/image148.png)

**3.2.4.3. 품질 프로파일**

**3.2.4.3.1. 품질 프로파일 생성**

1. 품질 관리 메뉴에서 품질 프로파일을 선택하여 품질 프로파일 대시보드로 이동한다.

   ![](../../.gitbook/assets/image149.jpg)

2. 우측 상단의 “생성” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image150.png)

3. 품질 프로파일 명을 입력하고, 개발언어를 선택하여 “생성” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image151.png)

4. 품질 프로파일이 생성된 것을 확인한다.

   ![](../../.gitbook/assets/image152.png)

**3.2.4.3.2. 품질 프로파일 복제**

1. 품질 프로파일 대시보드에서 “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image153.png)

2. 복제할 품질 프로파일의 이름을 입력하고, “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image154.png)

3. 복제된 품질 프로파일을 확인한다.

   ![](../../.gitbook/assets/image155.png)

**3.2.4.3.3. 품질 프로파일 수정**

1. 품질 프로파일 대시보드에서 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image156.png)

2. 수정할 품질 프로파일 팝업창이 뜨면 품질 프로파일 명을 수정하고 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image157.png)

3. 품질 프로파일 명이 수정되었음을 확인한다.

   ![](../../.gitbook/assets/image158.png)

**3.2.4.3.4. 품질 프로파일 프로젝트 연결**

1. 품질 프로파일 대시보드에서 연결된 프로젝트 항목을 확인한다. \(첫 번째 사진은 테스트 Job 구성 조회 시 품질 프로파일을 Default-QualityProfile 로 설정한 것이다. 그러므로 두 번째 사진에서 예시로 새로 생성한 품질 프로파일\[QualityProfile\_hrjin\]에는 연결된 프로젝트가 보이지 않는다.\)

   ![](../../.gitbook/assets/image159%20%281%29%20%281%29.jpg)

2. 연결된 프로젝트가 없을 경우 “미연결” 탭을 클릭한다.

   ![](../../.gitbook/assets/image160.png)

3. 해당 품질 프로파일과 연결할 테스트 Job 프로젝트를 선택한다. 품질 프로파일 1개 당 여러 프로젝트 연결이 가능하다.

   ![](../../.gitbook/assets/image161.png)

   **3.2.4.3.5. 품질 프로파일 삭제**

4. 품질 프로파일 대시보드에서 “삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image162.png)

5. 품질 프로파일이 삭제되었음을 확인한다.

   ![](../../.gitbook/assets/image163.png)

**3.2.4.4. 품질 게이트**

**3.2.4.4.1. 품질 게이트 생성**

1. 품질 관리 메뉴에서 품질 게이트를 선택하여 품질 게이트 대시보드로 이동한다.

   ![](../../.gitbook/assets/image164.jpg)

2. 우측 상단의 “생성” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image165.png)

3. 품질 게이트 명을 입력하고, “생성” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image166.png)

4. 품질 게이트가 생성된 것을 확인한다.

   ![](../../.gitbook/assets/image167.jpg)

**3.2.4.4.2. 품질 게이트 복제**

1. 품질 게이트 대시보드에서 “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image168.png)

2. 복제할 품질 게이트의 이름을 입력하고, “복제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image169.png)

3. 복제된 품질 게이트를 확인한다.

   ![](../../.gitbook/assets/image170.png)

**3.2.4.4.3. 품질 게이트 수정**

1. 품질 게이트 대시보드에서 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image171.png)

2. 수정할 품질 게이트 팝업창이 뜨면 품질 게이트 명을 수정하고 “수정” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image172.png)

3. 품질 게이트 명이 수정되었음을 확인한다.

   ![](../../.gitbook/assets/image173.png)

**3.2.4.4.4. 품질 게이트 조건추가**

1. 품질 게이트 대시보드에서 Job 테스트 시 통과 기준이 되는 조건을 설정할 수 있는 조건 추가 부분을 확인한다. 사용자가 직접 조건을 추가하고 기준 설정이 가능하다.

   ![](../../.gitbook/assets/image174.png)

2. 조건에 따라 어느 기준 이상/이하가 될 시에 테스트를 통과시키도록 한다.

   ![](../../.gitbook/assets/image175.png)

3. 현재 ‘Default-QualityGate’ 가 기본 품질 게이트로 설정되어 있는데 이것을 참고로 한다.

   ![](../../.gitbook/assets/image176.jpg)

**3.2.4.4.5. 품질 게이트 프로젝트 연결**

1. 품질 게이트 대시보드에서 연결된 프로젝트 항목을 확인한다. \(첫 번째 사진은 테스트 Job 구성 조회 시 품질 게이트를 test-QualityGate 로 설정한 것이다. 그러므로 두번째 사진에서 연결된 프로젝트에 test2 파이프라인의 테스트 Job 이 보인다.\)  


   ![](../../.gitbook/assets/image159%20%281%29.jpg)

2. 품질 게이트도 품질 프로파일과 마찬가지로 품질 게이트 1개당 여러 프로젝트 연결이 가능하다.

   ![](../../.gitbook/assets/image177.png)

**3.2.4.4.6. 품질 게이트 삭제**

1. 품질 게이트 대시보드에서 “삭제” 버튼을 클릭한다.

   ![](../../.gitbook/assets/image178.png)

2. 품질 게이트가 삭제되었음을 확인한다.

   ![](../../.gitbook/assets/image179.png)

