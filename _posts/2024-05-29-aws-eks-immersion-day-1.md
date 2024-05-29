---
layout: post
title: AWS EKS Immersion Day 1 워크샵
categories: kubernetes
tags: [docker, kubernetes, aws]
---

### 사전학습

#### 도커

- 이미지: 서비스 운영에 필요한 서버 프로그램, 소스코드 및 라이브러리 등을 묶은 형태
- 컨테이너: 이미지를 실행한 상태

#### 쿠버네티스

컨테이너 환경을 구성해 배포하다 보면, 배포할 컨테이너의 수가 많아지면서 컨테이너 오케스트레이션 툴의 필요성이 증가한다. 컨테이너 오케스트레이션 툴은 컨테이너의 네트워킹 및 관리를 자동화하여 효율성을 높여준다. 다양한 컨테이너 오케스트레이션 툴 중에서 대표적으로 사용되는 몇 가지는 Docker Swarm, Docker Compose, Apache Mesos 등이 있다. 그 중에서도 쿠버네티스(Kubernetes)가 많이 사용되는 이유는 다양한 플러그인을 쉽게 적용할 수 있기 때문이다.

#### 쿠버네티스 컴포넌트

![](/image/k8s-component.png)

쿠버네티스를 배포하면 클러스터를 얻는다. 그리고 클러스터는 노드들의 집합이다. 노드들은 크게 두 가지 유형으로 나눠진다.

- Control Plane
  - 구성: api 서버, 컨트롤러, 스케줄러(pod 배치), etcd(key-value 저장소, 쿠버네티스 클러스터 상태 저장)
  - 스케줄링을 수행하고, 클러스터 이벤트를 감지하고 반응하는 역할을 한다.
    - 하지만 설정이 많고 구성하기 어렵기 때문에 AWS에서 EKS라는 서비스로 쉽게 제공한다.
    - (vs AWS ECS: AWS에서 만든 완전 관리형 컨테이너 오케스트레이션 서비스)
  

- Data Plane
  - 구성: pod, kube-proxy(ip router table, 네트워크 담당), kubelet(노드의 상태를 control plane에 주기적으로 보내는 역할) 
  - AWS EC2
  - AWS Fargate: EC2와 달리 컨테이너와 1:1 매칭되는 완전 관리형 서비스

#### 쿠버네티스 Object

- Pod: 컨테이너 묶음
- Replica Set: Pod 묶음 
- Deployment: pod와 replica set에 대한 선언적 업데이트 제공 
  - 보통 Pod, Replica Set 보다 deployment를 통해 관리
- Service: pod들을 위한 고정 endpoint 제공
  - Cluster IP: 쿠버네티스 내부 IP
  - Node Port: 외부 port 개방
  - Load Balancer: 클라우드 제공 로드밸런서로 노출
- Ingress: 클러스터 외부에서 클러스터 내부 서비스로 HTTP와 HTTPS 경로 노출

### Hands on

Cloud9(AWS IDE 서비스)을 통해 미리 구축된 환경에서 실습했다.

#### ECR 및 EKS 생성

- 도커 이미지 만들기
- ECR에 이미지 올리기
  - ECR Repository 생성
    ```shell
    aws ecr create-repository \
    --repository-name demo-flask-backend \
    --image-scanning-configuration scanOnPush=true \
    --region ${AWS_REGION}

    # scanOnPush=true: 취약점 점검 옵션
    ```
  - 이미지 빌드 후 Repository에 저장
    ```shell
    # build
    docker build -t demo-flask-backend .
    # tag
    # lastest보다 빌드시간과 같이 유니크한 값을 사용하는게 일반적
    docker tag demo-flask-backend:latest $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/demo-flask-backend:latest
    # push
    docker push $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/demo-flask-backend:latest
    ```
- eksctl로 EKS 클러스터 생성
  - eks-demo-cluster.yaml 작성
    ```yaml
    ---
    apiVersion: eksctl.io/v1alpha5
    kind: ClusterConfig

    metadata:
      name: eks-demo # 생성할 EKS 클러스터명
      region: ${AWS_REGION} # 클러스터를 생성할 리전
      version: "1.27"

    vpc:
      cidr: "10.0.0.0/16" # 클러스터에서 사용할 VPC의 CIDR
      nat:
          gateway: HighlyAvailable

    managedNodeGroups:
      - name: node-group # 클러스터의 노드 그룹명
        instanceType: m5.large # 클러스터 워커 노드의 인스턴스 타입
        desiredCapacity: 3 # 클러스터 워커 노드의 갯수
        volumeSize: 20  # 클러스터 워커 노드의 EBS 용량 (단위: GiB)
        privateNetworking: true
        iam:
          withAddonPolicies:
              imageBuilder: true # Amazon ECR에 대한 권한 추가
              albIngress: true  # albIngress에 대한 권한 추가
              cloudWatch: true # cloudWatch에 대한 권한 추가
              autoScaler: true # auto scaling에 대한 권한 추가
              ebs: true # EBS CSI Driver에 대한 권한 추가

    cloudWatch:
      clusterLogging:
          enableTypes: ["*"]

    iam:
      withOIDC: true # 쿠버네티스에서 AWS 기능을 사용할 때 권한 체크 
    ```
  - 클러스터 배포 (15분 ~ 20분 소요)
    - AWS CloudFormation(코드형 인프라 관리 서비스)으로 던져져 클러스터가 생성 됨
    ```shell
    eksctl create cluster -f eks-demo-cluster.yaml
    ```
    - 3개의 AZ에 VPC를 생성
    - 각 AZ에 1개 노드 노드 생성
    - 각 AZ에 privat/public 두 개의 서브넷 생성 (private 서브넷은 NAT에 연결해 외부 통신 가능)
  - 배포가 완료되면, 아래의 명령을 통해 확인
    ```shell
    kubectl get pod -A
    # READY에 2/2는 pod안에 컨테이너가 2개 생성되야 한다는 의미
    NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
    kube-system   aws-node-4xt6r             2/2     Running   0          4d15h
    kube-system   aws-node-kshdc             2/2     Running   0          4d15h
    kube-system   aws-node-s5ms5             2/2     Running   0          4d15h
    kube-system   coredns-5b8cc885bc-h4w6f   1/1     Running   0          4d15h
    kube-system   coredns-5b8cc885bc-xrv7s   1/1     Running   0          4d15h
    kube-system   kube-proxy-7p2xk           1/1     Running   0          4d15h
    kube-system   kube-proxy-9lnqz           1/1     Running   0          4d15h
    kube-system   kube-proxy-mmx4q           1/1     Running   0          4d15h

    # 자격증명 확인 
    cat ~/.kube/config
    apiVersion: v1
    # ...
    clusters:
    - cluster:
        server: 10.0.0.1
      name: main.com
    - cluster:
        server: 127.0.0.1
      name: local.com
    contexts:
    - context:
        cluster: main.com
        user: admin
      name: admin@main.com
    - context:
        cluster: local.com
        user: account
      name: account@local.com
    # ...
    ```
  - 컨텍스트 변경
    - 클러스터와 user 관계를 매핑한 것
    - 워크샵을 위해 미리 구성된 클러스터와 새로 생성한 두 개의 클러스터가 존재
    ```shell
    kubectl config use-context account@local.com
    ```

> [Amazon EKS로 웹 애플리케이션 구축하기 (한글자료)](https://catalog.us-east-1.prod.workshops.aws/workshops/9c0aa9ab-90a9-44a6-abe1-8dff360ae428/ko-KR)


#### kube-ops-view 설치

쿠버네테스 클러스터 구성을 그래픽으로 보여주는 기능이다.

```shell
git clone https://github.com/dobal-production/eksworkshop-custom.git
cd ~/eksworkshop-custom/introduction/setup/05.kube-ops-view
./05.kube-ops-view-clb.sh
```

> [강사님 Githup repo](https://github.com/dobal-production/eksworkshop-custom)


#### 어플리케이션 배포  

- catalog component 배포
  ```shell
  # catalog component 확인
  ls ~/environment/eks-workshop/base-application/catalog
  configMap.yaml  # key-value 저장
  deployment.yaml
  kustomization.yaml
  namespace.yaml
  secrets.yaml  # secret 저장
  service-mysql.yaml
  service.yaml
  serviceAccount.yaml
  statefulset-mysql.yaml

  # 배포
  # -k 옵션: kustomization.yaml 파일을 읽어서 적용
  kubectl apply -k ~/environment/eks-workshop/base-application/catalog
  ```
  - 배포 확인
  ```shell
  kubectl get svc -n catalog                                                         
  NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
  catalog         ClusterIP   172.20.49.6    <none>        80/TCP     2m28s
  catalog-mysql   ClusterIP   172.20.7.250   <none>        3306/TCP   2m28s
  ```

#### 어플리케이션 네트워크  

- 어플리케이션 노출: 서비스는 기본적으로 Cluster IP 방식을 사용하므로, 외부 통신을 위한 작업이 필요 
  - 로드밸런서: 서비스를 로드밸런서로 노출(node port mode)
    - NLB(TCP 계층에서 작동하는 레이어4 로드 밸런서)에 연결
    - 로드밸런서 생성
      ```yaml
      # ~/environment/eks-workshop/modules/exposing/load-balancer/nlb/nlb.yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ui-nlb
        annotations:  # AWS와 쿠버네티스 명령을 연결
          service.beta.kubernetes.io/aws-load-balancer-type: external  # internal로 하면 private 존 안에 내부 통신만 가능
          service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing
          service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: instance  # 노드로 연결
        namespace: ui
      spec:
        type: LoadBalancer
        ports:
          - port: 80
            targetPort: 8080
            name: http
        selector:
          app.kubernetes.io/name: ui
          app.kubernetes.io/instance: ui
          app.kubernetes.io/component: service
      ```

      ```shell
      kubectl apply -k ~/environment/eks-workshop/modules/exposing/load-balancer/nlb
      ```
    - 서비스마다 로드밸런스를 생성하므로, http(s)만 사용한다면 ingress를 사용하는게 좋다.
  - Ingress: 서비스에 연결
    - ALB(http(s) 계층에서 작동하는 레이어7 로드밸런서) 사용
    - 로드밸런스 하나로 path(경로)를 이용해 라우팅
    - ingress 생성 
      ```yaml
      # ~/environment/eks-workshop/modules/exposing/ingress/creating-ingress/ingress.yaml
      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ui
        namespace: ui
        annotations:
          alb.ingress.kubernetes.io/scheme: internet-facing
          alb.ingress.kubernetes.io/target-type: ip
          alb.ingress.kubernetes.io/healthcheck-path: /actuator/health/liveness
      spec:
        ingressClassName: alb
        rules:
          - http:
              paths:
                - path: /  # 루트 80에 ui 연결
                  pathType: Prefix
                  backend:
                    service:
                      name: ui
                      port:
                        number: 80
      ```

      ```shll
      kubectl apply -k ~/environment/eks-workshop/modules/exposing/ingress/creating-ingress
      ```
    - 확인
      ```shell
      # 근본을 파헤칠 때, describe 명령 사용 
      $ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-ui`) == `true`].LoadBalancerArn' | jq -r '.[0]')
      $ TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --load-balancer-arn $ALB_ARN | jq -r '.TargetGroups[0].TargetGroupArn')
      $ aws elbv2 describe-target-health --target-group-arn $TARGET_GROUP_ARN
      {
          "TargetHealthDescriptions": [
              {
                  "Target": {
                      "Id": "10.42.180.183",
                      "Port": 8080,
                      "AvailabilityZone": "us-west-2c"
                  },
                  "HealthCheckPort": "8080",
                  "TargetHealth": {
                      "State": "healthy"
                  }
              }
          ]
      }
      ```
      `alb.ingress.kubernetes.io/target-type: ip` ip 모드를 지정했기 때문에, ui pod의 ip와 포트가 타겟에 등록됨
  - 로드밸런서(ip mode), 그림 3번에 해당 
    ![](/image/ip-mode-5a2f1be81ebf0ed8c08f825bfb1394c6.png) 
  
    - 로드밸런서 node port mode 설정에서 한줄만 변경
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: ip  # instance에서 ip로 변경
          service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing
          service.beta.kubernetes.io/aws-load-balancer-type: external
        name: ui-nlb
        namespace: ui
      ```

      ```shell
      kubectl apply -k ~/environment/eks-workshop/modules/exposing/load-balancer/ip-mode
      ```


> [EKS Workshop (영문자료)](https://www.eksworkshop.com)

