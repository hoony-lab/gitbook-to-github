# CaaS 단독 배포 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](caas.md#1) 1.1. [목적](caas.md#1.1) 1.2. [범위](caas.md#1.2) 1.3. [시스템 구성도](caas.md#1.3) 1.4. [참고자료](caas.md#1.4)​
2. 3. 4. 5. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서 \(Kubespray 설치 가이드\) 는 개방형 PaaS 플랫폼 고도화 및 개발자 지원 환경 기반의 Open PaaS에 배포되는 컨테이터 플랫폼을 설치하기 위한 Kubernetes Native를 Kubespray를 이용하여 설치하는 방법을 기술하였다.

PaaS-TA 5.5 버전부터는 Kubespray 기반으로 단독 배포를 지원한다. 기존 Container 서비스 기반으로 설치를 원할 경우에는 PaaS-TA 5.0 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 Kubernetes Native를 검증하기 위한 Kubespray 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

시스템 구성은 Kubernetes Cluster\(Master, Worker\)와 BOSH Inception\(DBMS, HAProxy, Private Registry\)환경으로 구성되어 있다. Kubespary를 통해 Kubernetes Cluster를 설치하고 BOSH release로 Database, Private registry 등 미들웨어 환경을 제공하여 Docker Image로 Kubernetes Cluster에 Container Platform 포털 환경을 배포한다. 총 필요한 VM 환경으로는 Master VM: 1개, Worker VM: 1개 이상, BOSH Inception VM: 1개가 필요하고 본 문서는 Kubernetes Cluster 환경을 구성하기 위한 Master VM 과 Worker VM 설치 내용이다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F101b69b68bcfcd2e29f0b5d3889a5195a0d74c62.png?alt=media)

### 1.4. 참고자료 <a id="1-4"></a>

> ​[https://kubespray.io](https://kubespray.io/) [https://github.com/kubernetes-sigs/kubespray](https://github.com/kubernetes-sigs/kubespray)​

## 2. Kubespray 설치 <a id="2-kubespray"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Ubuntu 18.04 환경에서 설치하는 것을 기준으로 하였다. Kubespray 설치를 위해서는 Ansible v2.9 +, Jinja 2.11+ 및 python-netaddr이 Ansible 명령을 실행할 시스템에 설치되어 있어야 하며 설치 가이드에 따라 순차적으로 설치가 진행된다.

Kubespray 설치에 필요한 주요 소프트웨어 및 패키지 Version 정보는 다음과 같다.

Kubernetes 공식 가이드 문서에서는 Cluster 배포 시 다음을 권고하고 있다.

* deb / rpm 호환 Linux OS를 실행하는 하나 이상의 머신 \(Ubuntu 또는 CentOS\)
* 머신 당 2G 이상의 RAM
* control-plane 노드로 사용하는 머신에 2 개 이상의 CPU
* 클러스터의 모든 시스템 간의 완전한 네트워크 연결

### 2.2. SSH Key 생성 및 배포 <a id="2-2-ssh-key"></a>

Kubespray 설치를 위해서는 SSH Key가 인벤토리의 모든 서버들에 복사되어야 한다. 본 설치 가이드에서는 RSA 공개키를 이용하여 SSH 접속 설정을 진행한다.

SSH Key 생성 및 배포 이후의 모든 설치과정은 Master Node에서 진행한다.

* Master Node에서 RSA 공개키를 생성한다.

  ```text
  $ ssh-keygen -t rsaGenerating public/private rsa key pair.Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa): [엔터키 입력]Enter passphrase (empty for no passphrase): [엔터키 입력]Enter same passphrase again: [엔터키 입력]Your identification has been saved in /home/ubuntu/.ssh/id_rsa.Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.The key fingerprint is:The key's randomart image is:+---[RSA 2048]----+|+o.              ||=+     . .       ||B.=   . = .      ||.E... .+ +       ||o.+. o  S .      ||..o...   o .     ||.o .. o + +      ||..    .BoB .o    ||+.   .+++.=+     |+----[SHA256]-----+
  ```

* 사용할 Master, Worker Node에 공개키를 복사한다.

  ```text
  # 출력된 공개키 복사$ cat ~/.ssh/id_rsa.pubssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAdc4dIUh1AbmMrMQtLH6nTNt6WZA9K5BzyNAEsDbbm8OzCYjGPFNexrxU2OyfHAUzLhs+ovXafX0RG5bvm44B04LH01maV8j32Vkag0DtNEiA96WjR9wpTeqfZy0Qwko9+TJOfK7lVT7+GCPm112pzU/t3i9oaptFdalGLYC+ib2+ViibkV0rZ8ds/zz/i0uzXDqvYl1HYfc7kA1CtinAimxV2FU/7WDTIj5HAfPnhyXPf+k1d3hPJEZ+T3qUmLnVpIXS2AHETPz29mu/I8EWUfc8/OVFJqS8RAyGghfnbFPrVEL3+jp/K6nwfX9nnpJWXvMtYenKwHI+mY8iuEYr [email protected]
  ```

* 사용할 Master, Worker Node의 authorized\_keys 파일 본문의 마지막 부분\(기존 본문 내용 아래 추가\)에 공개키를 복사한다.

```text
$ vi .ssh/authorized_keys​ex)ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDU+CSWd/bC4IfC+cuRDDJwAfjiPXAZAYFc7S8B8rUqAcuCPZUcbSZu5BIEAxZC+DCtpAJmtCGl/w7Gp1Wwij6hm/WlYeWapoqTe1yeLA/k0YhY0kQWuobfGlo9w7gxFKY8Aqtft+lRLhxteYc+/XxENFoq+eVFbX9jAOBbhM73K1oiV2YZcNAriLXixFYYVTmOPnJYUabJLi7E5ZEo3RaQ7Wol2fPPKQyvblwl9T5AoKF+/haWifeNEDHsd4XW9lveIRMsY3x7zUsnCtxzAlQKsw/eogKpyCc6E1GhmSTNy+K5fzhLgBJvl3J8t/+MKf8UGJA11pAn1L0vt56dTOdj aws-paasta-kube-keyssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAdc4dIUh1AbmMrMQtLH6nTNt6WZA9K5BzyNAEsDbbm8OzCYjGPFNexrxU2OyfHAUzLhs+ovXafX0RG5bvm44B04LH01maV8j32Vkag0DtNEiA96WjR9wpTeqfZy0Qwko9+TJOfK7lVT7+GCPm112pzU/t3i9oaptFdalGLYC+ib2+ViibkV0rZ8ds/zz/i0uzXDqvYl1HYfc7kA1CtinAimxV2FU/7WDTIj5HAfPnhyXPf+k1d3hPJEZ+T3qUmLnVpIXS2AHETPz29mu/I8EWUfc8/OVFJqS8RAyGghfnbFPrVEL3+jp/
```

### 2.3. Kubespray 다운로드 <a id="2-3-kubespray"></a>

2.3.부터는 Master Node에서만 진행을 하면 된다.\(Worker Node에는 더 이상 추가 작업이 없음\) Kubespray 설치에 필요한 Source File을 Download 받아 Kubespray 설치 작업 경로로 위치시킨다.

* * git clone 명령을 통해 다음 경로에서 Kubespray 다운로드를 진행한다. 본 설치 가이드에서의 Kubespray 버전은 v2.14.2 이다.

```text
$ git clone https://github.com/PaaS-TA/paas-ta-container-platform-deployment.git
```

### 2.4. Ubuntu, Python Package 설치 <a id="2-4-ubuntu-python-package"></a>

Kubespray 설치에 필요한 Ansible, Jinja 등 Python Package 설치를 진행한다.

* apt-get update를 진행한다.
* python3-pip Package를 설치한다.

```text
$ sudo apt-get install -y python3-pip
```

* Kubespray 설치경로 이동, pip를 이용하여 Kubespray 설치에 필요한 Python Package 설치를 진행한다.

```text
# AWS 환경 설치 시$ cd paas-ta-container-platform-deployment/standalone/aws​# Openstack 환경 설치 시$ cd paas-ta-container-platform-deployment/standalone/openstack​$ sudo pip3 install -r requirements.txt
```

### 2.5. Kubespray 파일 수정 <a id="2-5-kubespray"></a>

Kubespray inventory 파일에는 배포할 Master, Worker Node의 구성을 정의한다. 본 설치 가이드에서는 1개의 Master Node와 3개의 Worker Node, 1개의 etcd 배포를 기준으로 가이드를 진행하며 기본 CNI는 calico로 설정되어있다. \(기본 Cluster 환경 구성은 Master Node 1개와 Worker Node 1개 이상을 필요로 한다.\)

* mycluster 디렉토리의 inventory.ini 파일을 설정한다.

$ vi inventory/mycluster/inventory.ini

```text
# inventory.ini# {MASTER_HOST_NAME}, {WORKER_HOST_NAME} : 실제 Master, Worker Node hostname ($ hostname 명령어를 입력시 나오는 이름) # {MASTER_NODE_IP}, {WORKER_NODE_IP} : Master, Worker Node Private IP​# ex)# ip-10-0-0-1xx ansible_host=10.0.0.1xx ip=10.0.0.1xx etcd_member_name=etcd1# ip-10-0-0-2xx ansible_host=10.0.0.2xx ip=10.0.0.2xx​[all]{MASTER_HOST_NAME} ansible_host={MASTER_NODE_IP} ip={MASTER_NODE_IP} etcd_member_name=etcd1{WORKER_HOST_NAME1} ansible_host={WORKER_NODE_IP1} ip={WORKER_NODE_IP1}      # 사용할 WORKER_NODE 개수(1개 이상)에 따라 작성 {WORKER_HOST_NAME2} ansible_host={WORKER_NODE_IP2} ip={WORKER_NODE_IP2}{WORKER_HOST_NAME3} ansible_host={WORKER_NODE_IP3} ip={WORKER_NODE_IP3}​[kube-master]ip-10-0-0-1xx           #{MASTER_HOST_NAME}​[etcd]ip-10-0-0-1xx           #{MASTER_HOST_NAME}​[kube-node]ip-10-0-0-2xx           #{WORKER_HOST_NAME1}: 사용할 WORKER_NODE 개수(1개 이상)에 따라 작성{WORKER_HOST_NAME2}{WORKER_HOST_NAME3}​[calico-rr]​[k8s-cluster:children]kube-masterkube-nodecalico-rr
```

### 2.6. Kubespray 설치 <a id="2-6-kubespray"></a>

Ansible playbook을 이용하여 Kubespray 설치를 진행한다.

* 인벤토리 빌더로 Ansible 인벤토리 파일을 업데이트한다.

```text
# {MASTER_NODE_IP}, {WORKER_NODE_IP} : Master, Worker Node Private IP# {WORKER_NODE_IP}는 사용할 WORKER_NODE 개수(1개 이상)에 따라 작성​# ex)# declare -a IPS=(10.0.0.1x 10.0.0.2x 10.0.0.3x 10.0.0.4x)​$ declare -a IPS=({MASTER_NODE_IP} {WORKER_NODE_IP1} {WORKER_NODE_IP2} {WORKER_NODE_IP3})​# ${IPS[@]}는 변수가 아니라 명령어의 일부분이므로 주의$ CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

* Openstack 환경에 설치 시 추가적인 환경변수 설정이 필요하며 설정 파일을 다운로드 받아 자동으로 환경변수 등록이 가능하다.

```text
# Openstack UI 로그인 > 프로젝트 선택 > API 액세스 메뉴 선택 > OpenStack RC File 다운로드 클릭# 스크립트 파일 실행 후 Openstack 계정 패스워드 입력$ source {OPENSTACK_PROJECT_NAME}-openrc.shPlease enter your OpenStack Password for project admin as user admin: {패스워드 입력}
```

* Openstack 네트워크 인터페이스의 MTU값이 기본값 1450이 아닐 경우 CNI Plugin MTU 설정 변경이 필요하다.

```text
# MTU 확인 (ex mtu 1400)​$ ifconfigeth0: flags=4163  mtu 1400        inet 10.xx.xx.xx  netmask 255.255.255.0  broadcast 10.xx.xx.xxx        inet6 fe80::xxxx:xxxx:xxxx:xxxx  prefixlen 64  scopeid 0x20        ether fa:xx:xx:xx:xx:xx  txqueuelen 1000  (Ethernet)        RX packets 346254  bytes 1363064162 (1.3 GB)        RX errors 0  dropped 0  overruns 0  frame 0        TX packets 323884  bytes 191720735 (191.7 MB)        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0​$ vi ~/paas-ta-container-platform-deployment/standalone/openstack/inventory/mycluster/group_vars/k8s-cluster/k8s-net-calico.yml​calico_mtu: 1450 > calico_mtu: 1400
```

* Ansible playbook으로 Kubespray 배포를 진행한다. playbook은 root로 실행하도록 옵션을 지정한다. \(--become-user=root\)

```text
$ ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml
```

* Kubespray 설치 완료 후 Cluster 사용을 위하여 다음 과정을 실행한다.

```text
$ mkdir -p $HOME/.kube$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 2.7. Kubespray 설치 확인 <a id="2-7-kubespray"></a>

Kubernetes Node 및 kube-system Namespace의 Pod를 확인하여 Kubespray 설치를 확인한다.

```text
$ kubectl get nodesNAME            STATUS   ROLES    AGE     VERSIONip-10-0-0-140   Ready       2m      v1.18.6ip-10-0-0-148   Ready    master   2m54s   v1.18.6ip-10-0-0-175   Ready       2m      v1.18.6ip-10-0-0-54    Ready       2m      v1.18.6​$ kubectl get pods -n kube-systemNAME                                          READY   STATUS    RESTARTS   AGEcalico-kube-controllers-86744fd67d-nl9zn      1/1     Running   0          101scalico-node-gkmq8                             1/1     Running   1          2m2scalico-node-pwdpp                             1/1     Running   1          2m2scalico-node-qxbl5                             1/1     Running   1          2m2scalico-node-sfbgc                             1/1     Running   1          2m2scoredns-dff8fc7d-f2bhs                        1/1     Running   0          85scoredns-dff8fc7d-zfn8m                        1/1     Running   0          90sdns-autoscaler-66498f5c5f-vpw87               1/1     Running   0          87skube-apiserver-ip-10-0-0-148                  1/1     Running   0          3m14skube-controller-manager-ip-10-0-0-148         1/1     Running   0          3m14skube-proxy-27z6x                              1/1     Running   0          2m26skube-proxy-g9lwc                              1/1     Running   0          2m24skube-proxy-m67ts                              1/1     Running   0          2m24skube-proxy-vh7fr                              1/1     Running   0          2m24skube-scheduler-ip-10-0-0-148                  1/1     Running   1          3m14skubernetes-dashboard-667c4c65f8-htprk         1/1     Running   0          84skubernetes-metrics-scraper-54fbb4d595-dmmcr   1/1     Running   0          83snginx-proxy-ip-10-0-0-140                     1/1     Running   0          2m23snginx-proxy-ip-10-0-0-175                     1/1     Running   0          2m23snginx-proxy-ip-10-0-0-54                      1/1     Running   0          2m23snodelocaldns-pw8mq                            1/1     Running   0          85snodelocaldns-rt8sv                            1/1     Running   0          85snodelocaldns-sj4gh                            1/1     Running   0          85snodelocaldns-zhf2v                            1/1     Running   0          85s
```

## 3. Kubespray 삭제 \(참고\) <a id="3-kubespray"></a>

Ansible playbook을 이용하여 Kubespray 삭제를 진행한다.

```text
$ ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root reset.yml
```

## 4. 컨테이너 플랫폼 운영자 생성 및 Token 획득 \(참고\) <a id="4-token"></a>

### 4.1. Cluster Role 운영자 생성 및 Token 획득 <a id="4-1-cluster-role-token"></a>

Kubespray 설치 이후에 Cluster Role을 가진 운영자의 Service Account를 생성한다. 해당 Service Account의 Token은 운영자 포털에서 Super Admin 계정 생성 시 이용된다.

* Service Account를 생성한다.

```text
# {SERVICE_ACCOUNT} : 생성할 Service Account 명$ kubectl create serviceaccount {SERVICE_ACCOUNT} -n kube-system(ex. kubectl create serviceaccount k8sadmin -n kube-system)
```

* Cluster Role을 생성한 Service Account에 바인딩한다.

```text
$ kubectl create clusterrolebinding {SERVICE_ACCOUNT} --clusterrole=cluster-admin --serviceaccount=kube-system:{SERVICE_ACCOUNT}
```

* 생성한 Service Account의 Token을 획득한다.

```text
# {SECRET_NAME} : Mountable secrets 값 확인$ kubectl describe serviceaccount {SERVICE_ACCOUNT} -n kube-system​$ kubectl describe secret {SECRET_NAME} -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
```

### 4.2. Namespace 사용자 Token 획득 <a id="4-2-namespace-token"></a>

포털에서 Namespace 생성 및 사용자 등록 이후 Token값을 획득 시 이용된다.

* Namespace 사용자의 Token을 획득한다.

```text
# {SECRET_NAME} : Mountable secrets 값 확인# {NAMESPACE} : Namespace 명$ kubectl describe serviceaccount {SERVICE_ACCOUNT} -n {NAMESPACE}​$ kubectl describe secret {SECRET_NAME} -n {NAMESPACE} | grep -E '^token' | cut -f2 -d':' | tr -d " "
```

### 4.3. 컨테이너 플랫폼 Temp Namespace 생성 <a id="4-3-temp-namespace"></a>

컨테이너 플랫폼 배포 시 최초 Temp Namespace 생성이 필요하다. 해당 Temp Namespace는 포털 내 사용자 계정 관리를 위해 이용된다.

* Temp Namespace를 생성한다.

```text
$ kubectl create namespace paas-ta-container-platform-temp-namespace
```

## 5. Resource 생성 시 주의사항 <a id="5-resource"></a>

사용자가 직접 Resource를 생성 시 다음과 같은 prefix를 사용하지 않도록 주의한다.

