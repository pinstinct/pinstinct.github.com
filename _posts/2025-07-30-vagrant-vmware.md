---
layout: post
title: Sillicon Mac에서 쿠버네티스 실습 환경 설정
categories: infra
tags: [kubernetes, k8s, infra]
excerpt: Sillicon Mac에서 Vagrant, VMware를 이용해 쿠버네티스 클러스터를 구성한다.
---


## 설치

### 1. Vagrant

```sh
brew tap hashicorp/tap
brew install hashicorp/tap/hashicorp-vagrant
```

### 2. Rosetta 

```sh
/usr/sbin/softwareupdate --install-rosetta --agree-to-license
```

### 3. VMware Fusion

브로드컴 홈페이지 회원 가입 & 로그인 후 `메뉴에서 My Downloads - 중간에 Free Software Downloads available HERE 클릭 - VMware Fusion` 선택해 설치한다. 

[다운로드 페이지](https://support.broadcom.com/group/ecx/free-downloads)

### 4. Vagrant VMware Utility 

macOS, ARM64 선택해 다운받아 설치

[Install Vagrant VMware Utility](https://developer.hashicorp.com/vagrant/install/vmware)


### 5. Vagrant VMware provider plugin

```sh
vagrant plugin install vagrant-vmware-desktop
```

### 6. Vagrantfile 생성 및 실행 

#### box type 선택

[Discover Vagrant Boxes](https://portal.cloud.hashicorp.com/vagrant/discover)

다음과 같은 조건으로 검색
- providers: vmware_desktop
- architectures: arm64


#### Vagrantfile 생성 

```sh
vagrant init bento/ubuntu-20.04
```

위의 명령을 통해 생성된 Vagrantfile을 확인 후 추가 설정을 해준다.

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.box_check_update = false
  config.vm.provider "vmware_desktop" do |vmware|
      vmware.gui = false
      vmware.allowlist_verified = true
  end
end
```

#### Vagrant 실행 

Vagrantfile을 통해 실행하면, 자동으로 Vagrant box(VM 이미지 파일)를 다운받아 설치한다.

```sh
# 실행
vagrant up

# 상태확인
vagrant status

# ssh 접속
vagrant ssh

# 종료
vagrant halt

# 삭제 
vagrant destroy -f

# 일시정지
vagrant suspend

# 재개
vagrant resume

# 재부팅
vagrant reload
```


## 멀티 노드 클러스터 구성

#### Vagrantfile 정의 

```ruby
Vagrant.configure("2") do |config|
  # 공통 설정: Ubuntu ARM64 박스 지정
  config.vm.box = "bento/ubuntu-22.04" # ARM64 호환 우분투 박스
  config.vm.provider "vmware_desktop" do |v|
      v.cpus = 2 # 각 VM에 2 vCPU 할당
      v.memory = "4096" # 각 VM에 4GB RAM 할당
      v.vmx["ethernet0.virtualdev"] = "vmxnet3" # VMware Fusion 네트워크 어댑터
  end

  # 컨트롤플레인(마스터) 노드 정의
  config.vm.define "master" do |master|
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: "192.168.56.10"
    # 추가 프로비저닝(예: kubeadm 설치) 스크립트
    master.vm.provision "0", type: "shell", preserve_order: true, privileged: true, inline: <<-SHELL
echo "swap 기능 중지"
swapoff -a

echo "커널 모듈 설정"
# 커널: 하드웨어와 소프트웨어 중재 (하드웨어 자원 관리, 프로세스 스케줄링, 네트워크 통신 처리 등)
# 커널 모듈: 운영체제 커널에 동적으로 추가하거나 제거할 수 있는 확장 기능 (파일 시스템, 네트워크 드라이버, iptables, br_netfilter 등)
# iptables 사용하기 위한 커널 모듈 
sudo modprobe br_netfilter

# 부팅시, 로딩을 위한 설정 
cat <<-'EOF' >/etc/modules-load.d/kubernetes.conf
br_netfilter
EOF

cat <<-'EOF' >/etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

# 즉시 반영 
sudo sysctl --system

echo "CRI(containerd) 설치"
sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
# docker 인증서 설치
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
# apt 패키지 목록 업데이트 
sudo add-apt-repository -y "deb [arch=arm64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io
# containerd 기본 설정값 생성 
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
# 쿠버네티스의 Cgroups systemd를 사용할 것이므로 설정 변경 
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd

echo "kubeadm, kubelet, kubectl 설치"
# https://v1-32.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

echo "node ip에 생성한 ip 설정"
cat <<-'EOF' >/etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.56.10
EOF

echo "master 노드 생성"

# /vagrant 디렉토리는 Vagrant로 생성한 모든 가상머신에서 공유하는 디렉토리 
# Vagrantfile이 있는 로컬 디렉토리와도 공유 됨
OUTPUT_FILE=/vagrant/join.sh
rm -rf $OUTPUT_FILE
rm -rf /vagrant/.kube

# master 노드 생성 
# --pod-network-cidr: CNI로 flannel을 사용하므로 10.244.0.0/16 설정
# --control-plane-endpoint: 로컬 환경이라 도메인이 필요없으므로 master 노드의 IP 설정 
# --apiserver-advertise-address: master 노드 IP 설정 
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=192.168.56.10 --apiserver-advertise-address=192.168.56.10

# kubeadm join 명령을 스크립트 파일로 저장
sudo kubeadm token create --print-join-command > $OUTPUT_FILE
chmod +x $OUTPUT_FILE

echo "쿠버네티스 설정 및 CNI 설치"
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
cp -R $HOME/.kube /vagrant/.kube
cp -R $HOME/.kube /home/vagrant/.kube
sudo chown -R vagrant:vagrant /home/vagrant/.kube

# CNI(flannel) 설치 
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
kubectl completion bash >/etc/bash_completion.d/kubectl
echo 'alias k=kubectl' >>/home/vagrant/.bashrc
    SHELL
  end

  # 워커 노드 정의
  (1..3).each do |i|
    config.vm.define "worker#{i}" do |node|
      node.vm.hostname = "worker#{i}"
      node.vm.network "private_network", ip: "192.168.56.1#{i}"
      node.vm.provision "0", type: "shell", preserve_order: true, privileged: true, inline: <<-SHELL
echo "swap 기능 중지"
swapoff -a

echo "커널 모듈 설정"
# 커널: 하드웨어와 소프트웨어 중재 (하드웨어 자원 관리, 프로세스 스케줄링, 네트워크 통신 처리 등)
# 커널 모듈: 운영체제 커널에 동적으로 추가하거나 제거할 수 있는 확장 기능 (파일 시스템, 네트워크 드라이버, iptables, br_netfilter 등)
# iptables 사용하기 위한 커널 모듈 
sudo modprobe br_netfilter

# 부팅시, 로딩을 위한 설정 
cat <<-'EOF' >/etc/modules-load.d/kubernetes.conf
br_netfilter
EOF

cat <<-'EOF' >/etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF

# 즉시 반영 
sudo sysctl --system

echo "CRI(containerd) 설치"
sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
# docker 인증서 설치
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
# apt 패키지 목록 업데이트 
sudo add-apt-repository -y "deb [arch=arm64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io
# containerd 기본 설정값 생성 
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
# 쿠버네티스의 Cgroups systemd를 사용할 것이므로 설정 변경 
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd

echo "kubeadm, kubelet, kubectl 설치"
# https://v1-32.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

echo "node ip에 생성한 ip 설정"
cat <<-'EOF' >/etc/default/kubelet
KUBELET_EXTRA_ARGS=--node-ip=192.168.56.1#{i}
EOF

echo "join 명령 실행"
sudo bash /vagrant/join.sh
      SHELL
    end
  end
end
```


```sh
sudo vagrant up
sudo vagrant status

sudo vagrant ssh master
# 클러스터 확인
vagrant@master:~$ kubectl get node
# 설정한 ip 확인 
vagrant@master:~$ ip addr 
# root 권한 얻기 
vagrant@master:~$ sudo -i
# swap 비활성화 확인 
root@master:~# free -h
```


> 참고: [실리콘 Mac (M1 이상)에서 vagrant 활용하기](https://devops-james.tistory.com/271)

> 참고: [이재홍의 언제나 최신 Kubernetes - Unit 6.2 Vagrant와 kubeadm으로 가상머신에 클러스터 구축하기](https://pyrasis.com/jHLsAlwaysUpToDateKubernetes/Unit06/02)