# PAAS-TA\_PLATFORM\_INSTALL\_AUTOMATION\_USE\_MANUAL\_v1.0

### Table of Contents

1. [개요](paas-ta_platform_install_automation_use_manual_v1.0.md#1)
   * [목적](paas-ta_platform_install_automation_use_manual_v1.0.md#2)
   * [범위](paas-ta_platform_install_automation_use_manual_v1.0.md#3)
2. [플랫폼 설치 자동화 실행 환경 구성](paas-ta_platform_install_automation_use_manual_v1.0.md#4)
   * [플랫폼 설치 자동화 파일 관리](paas-ta_platform_install_automation_use_manual_v1.0.md#5)
   * [플랫폼 설치 자동화 사용 가이드](paas-ta_platform_install_automation_use_manual_v1.0.md#6)

## Deprecated

**※ 본 문서는 PaaS-TA 5.0.1 이하의 버전까지 지원한다.**

### Executive Summary

본 문서에서는 Inception VM Linux 환경\(Ubuntu 16.04\)을 기준으로 플랫폼 설치 자동화를 사용하는 방법에 대해 작성되었다.

## 1.  문서 개요

### 1.1.  목적

본 문서는 플랫폼 설치 자동화 시스템의 파일 구성 및 사용 절차에 대해 기술하였다

### 1.2.  범위

본 문서에서는 Linux 환경\(Ubuntu 16.04\)을 기준으로 인프라 환경에 플랫폼 설치 자동화의 설치하는 방법에 대해 작성되었다.

## 2.  플랫폼 설치 자동화 메뉴얼

플랫폼 설치 자동화는 플랫폼 설치자를 관리할 수 있는 플랫폼 관리자 관리메뉴와 지원 가능한 인프라를 제어할 수 있는 인프라 환경 관리 메뉴, PaaS-TA 플랫폼을 설치하기 위한 PaaS-TA 설치 자동화/이종 PaaS-TA 설치 자동화 메뉴로 구분된다.

| 분류 | 메뉴 | 설명 |
| :--- | :--- | :--- |
| 플랫폼 관리자 관리 | 코드 관리 | 공통 코드를 등록/수정/삭제 등 관리하는 화면 |
| 권한 관리 | 권한 정보를 등록/수정/삭제 등 관리하는 화면 |  |
| 사용자 관리 | 사용자 정보를 등록/수정/삭제 등 관리하는 화면 |  |
| 인프라 환경 관리 | 인프라 계정 관리 | 인프라 계정을 등록/수정/삭제 등 관리하는 화면 |
| AWS 관리 | AWS 인프라 리소스를 등록/수정/삭제 등 관리하는 화면 |  |
| Openstack 관리 | Openstack 인프라 리소스를 등록/수정/삭제 등 관리하는 화면 |  |
| Azure 관리 | Azure 인프라 리소스를 등록/수정/삭제 등 관리하는 화면 |  |
| PaaS-TA 설치 자동화 | 환경 설정 관리 | 디렉터/스템셀/릴리즈/인프라 환경 설정/디렉터 인증서 관리 등 PaaS-TA 플랫폼을 설치하기 위해 필요한 환경을 설정하는 메뉴 모음 |
| 플랫폼 설치 | Micro-Bosh, PaaS-TA, 서비스팩을 설치하는 메뉴 모음 |  |
| 배포 정보 조회 | 기본 디렉터를 통해 관리가 가능한 메뉴 모음 |  |
| 이종 PaaS-TA 설치 자동화 | 환경 설정 관리 | 디렉터/스템셀/릴리즈/인프라 환경 설정/디렉터 인증서 관리 등 PaaS-TA 플랫폼을 설치하기 위해 필요한 환경을 설정하는 메뉴 모음 |
| 이종 BOOTSTRAP | BOOTSTRAP 설치 관련 메뉴 모음 |  |
| 이종 CF-DEPLOYMENT | PaaS-TA 설치 관련 메뉴 모음 |  |
| 배포 정보 조회 | 디렉터를 통해 관리가 가능한 메뉴 모음 |  |

### 2.2.  플랫폼 설치 자동화 파일 관리

플랫폼 설치 관리자에서 파일 관리라 함은 배포에 필요한 스템셀과 릴리즈 그리고 배포 파일 관리를 의미한다. 플랫폼 설치 자동화 실행 시 실행 계정의 Home 디렉토리에 .bosh\_plugin 디렉토리를 생성하고 배포에 필요한 스템셀, 릴리즈, 인증서, Manifest 파일을 관리하도록 기준 디렉토리가 결정되어 있다

#### 1.  플랫폼 설치 자동화 설치 구성

| 설정 디렉 토리 | 설명 |
| :--- | :--- |
| {HOME}/.bosh\_plugin | 플랫폼 설치 자동화가 사용하는 기준 디렉토리 |
| {HOME}/.bosh\_plugin/stemcell | 스템셀 관리 디렉토리 |
| {HOME}/.bosh\_plugin/release | 릴리즈 관리 디렉토리 |
| {HOME}/.bosh\_plugin/deployment | 배포 관리 디렉토리 |
| {HOME}/.bosh\_plugin/deployment/manifest | 서비스팩 Manifest 관리 디렉토리 |
| {HOME}/.bosh\_plugin/lock | 스템셀, 릴리즈, 배포 등을 수행 시 lock 관리 디렉토리 |
| {HOME}/.bosh\_plugin/temp | 임시 디렉토리 |
| {HOME}/.bosh\_plugin/credential | 플랫폼 설치 자동화 BOSH 설치 인증서 관리 디렉토리 |
| {HOME}/.bosh\_plugin/cf\_credential | 플랫폼 설치 자동화 CF Deployment 설치 인증서 관리 디렉토리 |
| {HOME}/.bosh\_plugin/hybrid\_credential | 이종 플랫폼 설치 자동화 BOSH 설치 인증서 관리 디렉토리 |
| {HOME}/.bosh\_plugin/hybrid\_cf\_credential | 이종 플랫폼 설치 자동화 CF Deployment 설치 인증서 관리 디렉토리, 실제 CF의 디렉토리 |

**- 플랫폼 설치 자동화를 이용해서 다운로드 된 스템셀과 생성된 배포 파일은 해당 디렉토리에 각각 다운로드 또는 생성되어 관리된다.**

**- 플랫폼 설치 자동화에서는 cf\_credential, hybrid\_cf\_credential 폴더에서 cf-deployment/paasta의 인증서 정보를 관리하고 PaaS-TA 포털 설치 시 사용되는 uaa\_database\_paasword/uaa\_client\_password/cc\_database\_password 등의 정보를 확인할 수 있다.**

## 2.3.  플랫폼 설치 자동화 사용 & 활용 가이드

[플랫폼 설치 자동화를 통해 AWS PaaS-TA 설치 가이드](paas-ta_platform_install_automation_aws_v1.0.md)

[플랫폼 설치 자동화를 통해 Openstack PaaS-TA 설치 가이드](paas-ta_platform_install_automation_openstack_v1.0.md)

[플랫폼 설치 자동화를 통해 Azure PaaS-TA 설치 가이드](paas-ta_platform_install_automation_azure_v1.0.md)

[플랫폼 설치 자동화를 통해 Google PaaS-TA 설치 가이드](paas-ta_platform_install_automation_google_v1.0.md)

[플랫폼 설치 자동화를 통해 vSphere PaaS-TA 설치 가이드](paas-ta_platform_install_automation_vsphere_v1.0.md)

[PaaS-TA 설치 완료 후 플랫폼 설치 자동화 활용 가이드](paas-ta_platform_install_automation_util_manual_v1.0.md)

[이종 플랫폼 설치 자동화를 통해 AWS-Openstack PaaS-TA 설치 가이드](paas-ta_install_automation_use_guide_hybrid_-ops_aws-_v1.0.md)

[이종 플랫폼 설치 자동화를 통해 Azure-Openstack PaaS-TA 설치 가이드](paas-ta_install_automation_use_guide_hybrid_-ops_azure-_v1.0.md)

