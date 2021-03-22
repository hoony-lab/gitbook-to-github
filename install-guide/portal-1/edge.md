# Edge 배포 설치 가이드

## Table of Contents <a id="table-of-contents"></a>

1. ​[문서 개요](edge.md#1) 1.1. [목적](edge.md#1.1) 1.2. [범위](edge.md#1.2) 1.3. [시스템 구성도](edge.md#1.3) 1.4. [참고자료](edge.md#1.4)​
2. 3. 4. 5. 6. 
## 1. 문서 개요 <a id="1"></a>

### 1.1. 목적 <a id="1-1"></a>

본 문서 \(KubeEdge 설치 가이드\) 는 개방형 PaaS 플랫폼 고도화 및 개발자 지원 환경 기반의 Open PaaS에 배포되는 컨테이터 플랫폼을 설치하기 위한 KubeEdge를 설치하는 방법을 기술하였다.

PaaS-TA 6.0 버전부터는 KubeEdge 기반으로 단독 배포를 지원한다. 기존 Container 서비스 기반으로 설치를 원할 경우에는 PaaS-TA 5.0 이하 버전의 문서를 참고한다.

### 1.2. 범위 <a id="1-2"></a>

설치 범위는 KubeEdge를 검증하기 위한 기본 설치를 기준으로 작성하였다.

### 1.3. 시스템 구성도 <a id="1-3"></a>

시스템 구성은 Kubernetes Cluster\(Master, Worker\)와 BOSH Inception\(DBMS, HAProxy, Private Registry\)환경으로 구성되어 있다. Kubeadm를 통해 Kubernetes Cluster를 설치하고 Kubernetes 환경에 KubeEdge를 설치한다. BOSH release로는 Database, Private registry 등 미들웨어 환경을 제공하여 Docker Image로 Kubernetes Cluster에 Container Platform 포털 환경을 배포한다. 총 필요한 VM 환경으로는 Master VM: 1개, Worker VM: 1개 이상, BOSH Inception VM: 1개가 필요하고 본 문서는 Kubernetes Cluster 환경을 구성하기 위한 Master VM 과 Worker VM 설치 내용이다.

![](https://gblobscdn.gitbook.com/assets%2F-MUwPz1AA_o-VOi2V3xJ%2Fsync%2F101b69b68bcfcd2e29f0b5d3889a5195a0d74c62.png?alt=media)

### 1.4. 참고자료 <a id="1-4"></a>

> ​[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/) [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/) [https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/) [https://kubeedge.io/en/docs/](https://kubeedge.io/en/docs/) [https://github.com/kubeedge/kubeedge](https://github.com/kubeedge/kubeedge)​

## 2. KubeEdge 설치 <a id="2-kubeedge"></a>

### 2.1. Prerequisite <a id="2-1-prerequisite"></a>

본 설치 가이드는 Ubuntu 환경에서 설치하는 것을 기준으로 하였다. KubeEdge 설치를 위해서는 Docker, Kubernetes Native Cluster가 시스템에 배포되어 있어야 한다.

KubeEdge 설치에 필요한 주요 소프트웨어 및 패키지 Version 정보는 다음과 같다.

Kubernetes 공식 가이드 문서에서는 Cluster 배포 시 다음을 권고하고 있다.

* deb / rpm 호환 Linux OS를 실행하는 하나 이상의 머신 \(Ubuntu 또는 CentOS\)
* 머신 당 2G 이상의 RAM
* control-plane 노드로 사용하는 머신에 2 개 이상의 CPU
* 클러스터의 모든 시스템 간의 완전한 네트워크 연결

### 2.2. Docker 설치 <a id="2-2-docker"></a>

기본적으로 Kubernetes는 컨테이너 런타임 인터페이스 \(CRI\) 를 사용하여 선택한 컨테이너 런타임과 상호작용을 한다.

컨테이너 런타임에는 Docker, containerd, CRI-O 가 존재하며 본 설치 가이드에서는 Docker를 기준으로 설치를 진행한다.

설치대상은 전체 Node 대상이며 본 설치 가이드에서는 19.03.12 버전으로 설치를 진행한다.

* * Docker 설치에 필요한 Package 설치를 진행한다.

  ```text
  $ sudo apt-get install -y \  apt-transport-https \  ca-certificates \  curl \  gnupg-agent \  software-properties-common
  ```

* Docker Download 및 설치를 위한 apt-key 및 apt-repository를 추가한다.

```text
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -​$ sudo apt-key fingerprint 0EBFCD88​$ sudo add-apt-repository \   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \   $(lsb_release -cs) \   stable"
```

* 설치 가능한 Docker 버전 정보를 확인한다.

```text
$ apt-cache madison docker-ce docker-ce | 5:19.03.13~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.12~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.11~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.10~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.9~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.8~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.7~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.6~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.5~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.4~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.3~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.2~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.1~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:19.03.0~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.9~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.8~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.7~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.6~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.5~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.4~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.3~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.2~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.1~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 5:18.09.0~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 18.06.3~ce~3-0~ubuntu | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 18.06.2~ce~3-0~ubuntu | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 18.06.1~ce~3-0~ubuntu | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 18.06.0~ce~3-0~ubuntu | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages docker-ce | 18.03.1~ce~3-0~ubuntu | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
```

* Docker 설치를 진행한다.

```text
# {VERSION_STRING} : Version 정보. (ex : 5:19.03.12~3-0~ubuntu-bionic)$ sudo apt-get install -y docker-ce={VERSION_STRING} docker-ce-cli={VERSION_STRING} containerd.io
```

* Docker 설치 후 사용자에 권한을 부여한다.

```text
# {USER_NAME} : 현재 사용자$ sudo usermod -aG docker {USER_NAME}
```

### 2.3. kubeadm, kubectl, kubelet 설치 <a id="2-3-kubeadm-kubectl-kubelet"></a>

KubeEdge 설치를 위해서는 Master Node에 Kubernetes Cluster가 배포되어있어야 한다. Cluster를 배포하기 위해 전체 Node에 kubeadm, kubectl, kubelet 설치가 진행되어야한다.

* apt-get update 및 필요한 Package 설치를 진행한다.

```text
$ sudo apt-get update && sudo apt-get install -y apt-transport-https curl
```

* kubeadm, kubectl, kubelet Download 및 설치를 위한 apt-key 및 apt-repository를 추가한다.

```text
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -​$ cat <deb https://apt.kubernetes.io/ kubernetes-xenial mainEOF
```

* apt-get update 및 kubeadm, kubectl, kubelet 설치를 진행한다. 본 설치 가이드에서는 v1.18.6 기준으로 설치를 진행한다.

```text
$ sudo apt-get update​$ sudo apt-get install -y kubelet=1.18.6-00 kubeadm=1.18.6-00 kubectl=1.18.6-00
```

* kubeadm, kubectl, kubelet Package를 자동으로 설치, 업그레이드, 제거하지 않도록 고정한다.

```text
$ sudo apt-mark hold kubelet kubeadm kubectl
```

### 2.4. Kubernetes Native Cluster 배포 <a id="2-4-kubernetes-native-cluster"></a>

KubeEdge 설치를 위해서는 Master Node에 Kubernetes Cluster가 배포되어있어야 한다.

* Master Node에 Kubernetes Cluster 배포를 진행한다. Cluster 배포는 kubeadm을 통해 진행하며 배포 완료 후 출력되는 kubeadm join 명령어는 KubeEdge 설치에서는 사용하지 않는다.

```text
# {MASTER_NODE_IP} : Master Node Private IP# --pod-network-cidr=10.244.0.0/16은 flannel CNI 설치 시 설정값$ sudo kubeadm init --apiserver-advertise-address={MASTER_NODE_IP} --pod-network-cidr=10.244.0.0/16
```

* Cluster 배포 완료 후 사용을 위하여 다음 과정을 진행한다.

```text
$ mkdir -p $HOME/.kube​$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config​$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

* 추후 keadm 사용을 위해 root 계정으로 전환하여 동일한 과정을 진행한다.

```text
$ sudo su -​# mkdir -p $HOME/.kube​# cp -i /etc/kubernetes/admin.conf $HOME/.kube/config​# chown $(id -u):$(id -g) $HOME/.kube/config​# exit
```

* KubeEdge에서는 본 설치 가이드 작성 시점에 CNI를 지원하지 않으나 Master Node 구성 시 CNI Plugin이 배포되지 않으면 CoreDNS Pod가 Pending 상태를 유지하는 이슈가 발생한다. 따라서 Master Node에는 CNI Pod 배포가 필요하다. 본 설치 가이드에서는 flannel을 사용한다.

  > CNI 이슈 : [https://github.com/kubeedge/kubeedge/issues/2083](https://github.com/kubeedge/kubeedge/issues/2083)​

```text
# CNI Plugin 배포 전 CodrDNS 확인, Pending 상태로 확인$ kubectl get pods -n kube-systemNAME                                   READY   STATUS     RESTARTS   AGEcoredns-66bff467f8-s6wdg               0/1     Pending    0          4m6scoredns-66bff467f8-wzzgk               0/1     Pending    0          4m6setcd-ip-10-0-0-96                      1/1     Running    0          4m21skube-apiserver-ip-10-0-0-96            1/1     Running    0          4m21skube-controller-manager-ip-10-0-0-96   1/1     Running    0          4m21skube-proxy-lsjrg                       1/1     Running    0          4m6skube-scheduler-ip-10-0-0-96            1/1     Running    0          4m21s​# CNI Plugin 배포$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml​# CNI Plugin 배포 후 CodrDNS 확인, Running 상태로 확인$ kubectl get pods -n kube-systemNAME                                   READY   STATUS    RESTARTS   AGEcoredns-66bff467f8-s6wdg               1/1     Running   0          8m26scoredns-66bff467f8-wzzgk               1/1     Running   0          8m26setcd-ip-10-0-0-96                      1/1     Running   0          8m41skube-apiserver-ip-10-0-0-96            1/1     Running   0          8m41skube-controller-manager-ip-10-0-0-96   1/1     Running   0          8m41skube-flannel-ds-amd64-dqf2w            1/1     Running   0          4m31skube-proxy-lsjrg                       1/1     Running   0          8m26skube-scheduler-ip-10-0-0-96            1/1     Running   0          8m41s
```

* 이후 Worker Node에 배포되지 않도록 CNI Plugin의 DaemonSet yaml 수정을 진행한다.

```text
$ kubectl edit daemonsets.apps -n kube-system kube-flannel-ds-amd64
```

* spec.template.spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution.nodeSelectorTerms.matchExpressions 경로에 아래 내용을 추가한다.

```text
- key: node-role.kubernetes.io/edge  operator: DoesNotExist
```

### 2.5. KubeEdge keadm 설치 <a id="2-5-kubeedge-keadm"></a>

KubeEdge 설치를 위한 keadm 설치를 진행한다. keadm 실행 시 Super User 혹은 root 권한이 필요하므로 root 권한으로 설치를 진행한다.

* root 계정으로 전환 후 전체 Master, Worker Node에 keadm 다운로드 및 설치를 진행한다.

```text
$ sudo su -​# git clone https://github.com/PaaS-TA/paas-ta-container-platform-deployment.git​# cd paas-ta-container-platform-deployment/edge​# cp keadm /usr/bin/keadm
```

### 2.6. KubeEdge CloudCore 설치 <a id="2-6-kubeedge-cloudcore"></a>

본 항목부터 Master Node, Worker Node의 명칭을 KubeEdge 공식 가이드에 맞춰 각각 Cloud Side, Edge Side로 명시한다. KubeEdge Cloud Side에 CloudCore를 설치하여 설정을 진행한다.

* keadm init 명령으로 Cloud Side에 CloudCore 설치를 진행한다.

```text
# {CLOUD_SIDE_IP} : Cloud Side Private IP# keadm init --advertise-address={CLOUD_SIDE_IP} --master=https://{CLOUD_SIDE_IP}:6443 --kubeedge-version 1.4.0
```

* Edge Side에 EdgeCore를 설치하기 위한 Token값을 가져온다.

### 2.7. KubeEdge EdgeCore 설치 <a id="2-7-kubeedge-edgecore"></a>

KubeEdge Edge Side에 EdgeCore를 설치하여 설정을 진행한다.

* keadm join 명령으로 Edge Side에 EdgeCore 설치를 진행한다.

```text
# {CLOUD_SIDE_IP} : Cloud Side Private IP# {INTERFACE_NAME} : 실제 Edge Side에서 사용중인 인터페이스 이름 (ex: ens5)# {GET_TOKEN} : Cloud Side에서 CloudCore 설치 이후 호출한 Token 값# keadm join --cloudcore-ipport={CLOUD_SIDE_IP}:10000 --interfacename={INTERFACE_NAME} --token={GET_TOKEN} --kubeedge-version 1.4.0
```

### 2.8. kubectl logs 기능 활성화 <a id="2-8-kubectl-logs"></a>

KubeEdge v1.4.0 에서는 기본적으로 kubectl logs 명령을 사용할 수 없는 이슈가 존재한다. 본 설치 가이드에서는 해당 기능을 활성화 하기 위한 설정 가이드를 제공한다.

* Cloud Side에서 kubernetes ca.crt 및 ca.key 파일을 확인한다.

```text
# ls /etc/kubernetes/pki/
```

* Cloud Side에서 CLOUDCOREIPS 환경변수 설정 및 확인을 진행한다. \(HA Cluster 구성 시 VIP 설정\)

```text
# {CLOUD_SIDE_IP} : Cloud Side Private IP# export CLOUDCOREIPS="{CLOUD_SIDE_IP}"​# echo $CLOUDCOREIPS
```

* Cloud Side에서 certgen.sh 다운로드 및 인증서 생성을 진행한다.

```text
# cd /etc/kubeedge​# wget https://raw.githubusercontent.com/kubeedge/kubeedge/master/build/tools/certgen.sh​# chmod +x certgen.sh​# /etc/kubeedge/certgen.sh stream
```

* Cloud Side에서 iptables을 설정한다.

```text
# iptables -t nat -A OUTPUT -p tcp --dport 10350 -j DNAT --to $CLOUDCOREIPS:10003
```

* Cloud Side에서 cloudcore.yaml 파일을 수정한다. \(enable: true 로 변경\)

```text
# vi /etc/kubeedge/config/cloudcore.yaml​cloudStream:  enable: true  streamPort: 10003  tlsStreamCAFile: /etc/kubeedge/ca/streamCA.crt  tlsStreamCertFile: /etc/kubeedge/certs/stream.crt  tlsStreamPrivateKeyFile: /etc/kubeedge/certs/stream.key  tlsTunnelCAFile: /etc/kubeedge/ca/rootCA.crt  tlsTunnelCertFile: /etc/kubeedge/certs/server.crt  tlsTunnelPrivateKeyFile: /etc/kubeedge/certs/server.key  tunnelPort: 10004
```

* Edge Side에서 edgecore.yaml 파일을 수정한다. \(enable: true, server: {CLOUD\_SIDE\_IP}:10004\)

```text
# vi /etc/kubeedge/config/edgecore.yaml​# {CLOUD_SIDE_IP} : Cloud Side Private IP​edgeStream:  enable: true  handshakeTimeout: 30  readDeadline: 15  server: {CLOUD_SIDE_IP}:10004  tlsTunnelCAFile: /etc/kubeedge/ca/rootCA.crt  tlsTunnelCertFile: /etc/kubeedge/certs/server.crt  tlsTunnelPrivateKeyFile: /etc/kubeedge/certs/server.key  writeDeadline: 15
```

* Cloud Side에서 cloudcore를 재시작한다.

```text
# pkill cloudcore# nohup cloudcore > cloudcore.log 2>&1 &
```

* KubeEdge는 EdgeMesh를 사용하여 통신하나 현재 NodePort를 사용할 수 없는 이슈가 확인되어 kube-proxy를 사용하여야 NodePort가 정상 동작한다. kube-proxy 배포되어 있을 경우 edgecore 재시작이 불가능하므로 edgecore 재시작 시 kube-proxy 배포 여부를 우회할 수 있는 방법을 기술한다.
* edgecore.service 파일을 수정한다.

```text
$ sudo vi /etc/kubeedge/edgecore.service
```

* edgecore.service 파일의 \[Service\]에 다음을 추가한다.

```text
Environment="CHECK_EDGECORE_ENVIRONMENT=false"
```

* Edge Side에서 edgecore를 재시작한다.

```text
# systemctl daemon-reload# systemctl restart edgecore.service
```

### 2.9. KubeEdge 설치 확인 <a id="2-9-kubeedge"></a>

Kubernetes Node 및 kube-system Namespace의 Pod를 확인하여 KubeEdge 설치를 확인한다.

```text
$ kubectl get nodesNAME            STATUS   ROLES        AGE   VERSIONip-10-0-0-107   Ready    agent,edge   36m   v1.18.6-kubeedge-v1.4.0ip-10-0-0-157   Ready    agent,edge   35m   v1.18.6-kubeedge-v1.4.0ip-10-0-0-18    Ready    master       58m   v1.18.6ip-10-0-0-86    Ready    agent,edge   30m   v1.18.6-kubeedge-v1.4.0​$ kubectl get pods -n kube-systemNAME                                   READY   STATUS    RESTARTS   AGEcoredns-66bff467f8-qzhd9               1/1     Running   0          58mcoredns-66bff467f8-rnmhk               1/1     Running   0          58metcd-ip-10-0-0-18                      1/1     Running   0          58mkube-apiserver-ip-10-0-0-18            1/1     Running   0          58mkube-controller-manager-ip-10-0-0-18   1/1     Running   0          58mkube-flannel-ds-amd64-4xc69            1/1     Running   0          46mkube-proxy-bj6c6                       1/1     Running   0          45mkube-scheduler-ip-10-0-0-18            1/1     Running   0          58m
```

## 3. Kubernates Monitoring 도구 \(Metrics-server\) 배포 <a id="3-kubernates-monitoring-metrics-server"></a>

배포된 Resource의 CPU/Memory 사용량 등을 확인하기 위해서는 Metric-server 배포가 필요하며, 컨테이너 플랫폼 사용자포털에서도 정상적인 운용을 위해서는 필수적으로 배포되어야 한다. 또한 KubeEdge에서 Metrics-Server 배포 시 2.8. kubectl logs 기능 활성화 가 필수적으로 진행되어야 한다.

* Metrics-Server 배포를 위한 yaml 파일을 다운받는다.

```text
$ wget https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.0/components.yaml
```

* components.yaml 파일을 수정한다.

```text
$ vi components.yaml​# spec.template.spec 하위에 추가# {CLOUD_SIDE_HOSTNAME} : 실제 Cloud Side Hostname​    spec:      affinity:        nodeAffinity:          requiredDuringSchedulingIgnoredDuringExecution:            nodeSelectorTerms:            - matchExpressions:              - key: kubernetes.io/hostname                operator: In                values:                - {CLOUD_SIDE_HOSTNAME}      hostNetwork: true​# spec.template.spec.containers 하위 - args:에 추가​      containers:      - args:        - --v=2        - --kubelet-insecure-tls​# spec.template.spec 하위에 추가    ​      tolerations:      - key: "node-role.kubernetes.io"        operator: "Equal"        value: "master"        effect: "NoSchedule"
```

* 노드의 Taint 설정을 해제한다.

```text
$ kubectl taint nodes --all node-role.kubernetes.io/master-node/ip-10-0-0-251 untaintederror: taint "node-role.kubernetes.io/master" not found
```

* Metrics-Server를 배포한다.

```text
$ kubectl apply -f components.yaml
```

* Metrics 정보를 확인한다.

```text
$ kubectl top nodesNAME            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%ip-10-0-0-234   83m          4%     2438Mi          31%ip-10-0-0-97    38m          1%     2971Mi          38%​$ kubectl top pods -n kube-systemNAME                                    CPU(cores)   MEMORY(bytes)coredns-66bff467f8-8lngp                2m           6Micoredns-66bff467f8-cjqzv                2m           6Mietcd-ip-10-0-0-234                      11m          45Mikube-apiserver-ip-10-0-0-234            19m          286Mikube-controller-manager-ip-10-0-0-234   8m           40Mikube-flannel-ds-amd64-tstq9             2m           9Mikube-proxy-7cz4b                        1m           10Mikube-proxy-nntnh                        1m           10Mikube-scheduler-ip-10-0-0-234            3m           11Mimetrics-server-68cb9f9b79-xvkks         3m           12Mi
```

## 4. KubeEdge Reset \(참고\) <a id="4-kubeedge-reset"></a>

Cloud Side, Edge Side에서 KubeEdge를 중지한다. 필수구성요소는 삭제하지 않는다.

* Cloud Side에서 cloudcore를 중지하고 kubeedge Namespace와 같은 Kubernetes Master에서 KubeEdge 관련 리소스를 삭제한다.

```text
# keadm reset --kube-config=$HOME/.kube/config
```

* Edge Side에서 edgecore를 중지한다.

## 5. 컨테이너 플랫폼 운영자 생성 및 Token 획득 \(참고\) <a id="5-token"></a>

### 5.1. Cluster Role 운영자 생성 및 Token 획득 <a id="5-1-cluster-role-token"></a>

Kubespray 설치 이후에 Cluster Role을 가진 운영자의 Service Account를 생성한다. 해당 Service Account의 Token은 운영자 포털에서 Super Admin 계정 생성 시 이용된다.

* Service Account를 생성한다.

```text
# {SERVICE_ACCOUNT} : Service Account 명$ kubectl create serviceaccount {SERVICE_ACCOUNT} -n kube-system(eg. kubectl create serviceaccount k8sadmin -n kube-system)
```

* Cluster Role을 생성한 Service Account에 바인딩한다.

```text
$ kubectl create clusterrolebinding {SERVICE_ACCOUNT} --clusterrole=cluster-admin --serviceaccount=kube-system:{SERVICE_ACCOUNT}
```

* 생성한 Service Account의 Token을 획득한다.

```text
# {SECRET_NAME} : Mountable secrets 값 확인$ kubectl describe serviceaccount {SERVICE_ACCOUNT} -n kube-system​$ kubectl describe secret {SECRET_NAME} -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
```

### 5.2. Namespace 사용자 Token 획득 <a id="5-2-namespace-token"></a>

포털에서 Namespace 생성 및 사용자 등록 이후 Token값을 획득 시 이용된다.

* Namespace 사용자의 Token을 획득한다.

```text
# {SECRET_NAME} : Mountable secrets 값 확인# {NAMESPACE} : Namespace 명$ kubectl describe serviceaccount {SERVICE_ACCOUNT} -n {NAMESPACE}​$ kubectl describe secret {SECRET_NAME} -n {NAMESPACE} | grep -E '^token' | cut -f2 -d':' | tr -d " "
```

### 5.3. 컨테이너 플랫폼 Temp Namespace 생성 <a id="5-3-temp-namespace"></a>

컨테이너 플랫폼 배포 시 최초 Temp Namespace 생성이 필요하다. 해당 Temp Namespace는 포털 내 사용자 계정 관리를 위해 이용된다.

* Temp Namespace를 생성한다.

```text
$ kubectl create namespace paas-ta-container-platform-temp-namespace
```

## 6. Resource 생성 시 주의사항 <a id="6-resource"></a>

사용자가 직접 Resource를 생성 시 다음과 같은 prefix를 사용하지 않도록 주의한다.

