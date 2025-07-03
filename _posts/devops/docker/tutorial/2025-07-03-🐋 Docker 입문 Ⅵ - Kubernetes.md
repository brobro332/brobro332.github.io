---
title: 🐋 Docker 입문 Ⅵ - Kubernetes
date: 2025-07-03 18:44:00 +0900
categories:
  - Docker
tags:
  - Docker
  - 입문
---

![](/assets/image/Pasted%20image%2020250529002434.png)
> 📘 `『그림과 실습으로 배우는 Docker & Kubernetes』`를 읽고 정리한 글입니다.

### `Kubernetes`란?
- `Kubernetes`는 오픈소스로, `Container Orchestration` 도구의 일종이다.
- 여러 개의 `Container`를 지휘하는 도구를 말하며, `K8S`라고 줄여쓰기도 한다.
- 일반적인 프로그래머가 `Kubernetes`를 활발하게 사용할 일은 많지 않다.
- 왜냐하면 `Kubernetes`는 여러 대의 `Container`가 여러 개의 서버에 걸쳐 실행되는 것을 전제로 하기 때문이다.
- 다만 `Kubernetes`로 어떤 일을 할 수 있는 지 학습한다면 시스템을 개발할 때 유용할 수 있다.
- 여러 대의 서버에서 일일이 `Container`를 실행하고 관리하기는 쉬운 일이 아니다. 
- 20개의 `Container`를 만들려면 `docker run` 명령어를 20번 실행해야 하는데, `Kubernetes`는 이를 편리하게 해준다.
- `Kubernetes`는 `Docker Engine`과는 별개의 소프트웨어이다.
- `Kubernetes`는 공식 소프트웨어가 있는데도 `Third-party Software`가 여럿 나온다.
- 공식 소프트웨어가 주로 많이 쓰이고, 직접 구축하기는 까다롭다.
- 일반적으로는 `AWS` 등의 클라우드 컴퓨팅 서비스 `EC2`, `Fargate`(`Worker Node`), `EKS`(`Master Node`)를 사용해 구축하는 경우도 많다. 


### `Master Node`와 `Worker Node`
- `Master Node`는 `Worker Node`에서 실행되는 `Container`를 관리한다.
- `Worker Node`는 `Container`가 실제 동작하는 서버이다.
- `Master Node`와 `Worker Node`로 구성된 일군의 `Kubernetes` 시스템을 `Cluster`라고 한다.
- `Cluster`는 사람이 개입하지 않아도 `Master Node`에 설정된 내용에 따라 `Worker Node`가 관리되며 자율적으로 동작한다.
- `Master Node`에는 `Container` 등의 상태를 관리하기 위해 `etcd`라는 데이터베이스가 설치된다.
- `Worker Node`에는 `Docker Engine` 같은 `Container Engine`이 필요하다.
- `Master Node`는 제어판 역할을 하는 `Control Plain`을 통해 `Worker Node`를 관리한다.


### `Control Plain` 구성
- `kube-apiserver`: `kubectl`에서 명령을 전달 받아 외부와 통신하는 프로세스
- `kube-controller-manager`: 컨트롤러를 통합, 관리, 실행한다.
- `kube-scheduler`: `POD`를 `Worker Node`에 할당한다.
- `cloud-controller-manager`: 클라우드 서비스와 연동해 서비스를 생성한다.
- `etcd`: `Cluster` 관련 정보 전반을 관리하는 데이터베이스다.


### `Worker Node` 구성
- `kube-let`: `Master Node`의 `kube-scheduler`와 연동하여 `Worker Node`에 `POD`를 배치 및 실행한다. 
- 실행 중인 `POD`의 상태를 정기적으로 `Monitoring`하여 `kube-scheduler`에 통지한다.
- `kube-proxy`: `Network` 통신의 `Routing` 메커니즘


### `Kubernetes`는 항상 '바람직한 상태'를 유지한다.
- `"Container는 4개, 볼륨은 4개로 구성하라"`와 같이 어떤 '바람직한 상태'를 `YAML` 파일에 정의하고, 자동으로 `Container`를 생성하거나 삭제하면서 이 상태를 만들고 유지한다.
- `Kubernetes`의 정의 파일은 명령어로 수정이 가능하며, `etcd` 데이터베이스로 관리된다.
- 반면 `Docker-compose`는 `Monitoring` 기능이 없어서 `Container` 만들 때 외에는 관여하지도 않고, 명령어로 수정도 불가하며, 데이터베이스에서 관리하지도 않는다.
- `Kubernetes`가 정의 파일을 읽어 들인 후 명령어로 직접 처리를 하면 갖고 있는 정의 파일과 `etcd`에 저장된 정보가 일치하지 않으므로 주의해야 한다.
- `Container` 하나가 망가지면 해당 `Container`를 자동 삭제하고 새 `Container`로 대체한다.
- `Container`를 하나 줄이고 싶다면 삭제 명령어를 입력하는 게 아니라 파일에서 '바람직한 상태'를 수정해야 한다.
- 직접 삭제할 수도 있으나 `Docker` 명령어를 써서 삭제하면 `Kubernetes`가 `Container`가 하나 부족하다는 것을 탐지하고 `Container`를 보충해버린다.
- 그러므로 `Kubernetes`를 사용한다면 사람이 개입해서 `Container`를 관리해서는 안 된다.


### `Load Balancer`와 `Cloud Computing`
#### ✅ `Load Balancer`
- 한 대에 서버에 모든 요청이 집중되지 않도록 여러 대의 서버를 갖추고 요청을 각 서버에 분산하는 장치
- 서버에 들어오는 요청은 많을 때도 있고 적을 때도 있다. 
- 요청이 많을 때 갖춰 놓은 서버가 요청이 적을 때에는 그만큼 놀게 되기 때문에 비용이 낭비된다.
- 이런 문제를 해결해 주는 것이 `Docker`, `Kubernetes`, `Cloud Computing` 서비스다.

#### ✅ `Cloud Computing`
- `Cloud Computing` 서비스에는 `AWS`, `애저`, `GCP`이 있다.
- 요청이 많은 기간에 `Cloud`에서 서버를 늘리는 체제를 갖추면 된다.
- 이렇게 서버를 쉽게 늘리거나 줄일 수 있는 구조를 확장성이 좋다고 말한다.


### `Kubernetes`의 구성
- `Kubernetes`에서 `Container`는 `POD`라는 단위로 관리된다.
- `POD`는 `Container`와 볼륨을 함께 묶은 것으로, 기본적으로 `POD` 하나가 `Container` 하나지만 `Container`가 여러 개인 `POD`도 있을 수 있다.
- `POD`를 모은 것이 `Service`인데, `Load Balancer` 역할을 수행한다.
- 각 `Service`는 자동적으로 고정된 `IP` 주소(`Cluster IP`)를 부여 받아 이 주소로 들어오는 통신을 처리한다.
- 내부에 `POD`가 여러 개 있어도 밖에서는 하나의 주소만 존재하며 이 주소로 들어오는 요청을 `Service`가 적절히 분배하며, `Service`가 분배하는 통신은 한 `Worker Node` 안으로 국한된다.
- 예외적으로 여러 개의 `Worker Node`에 걸쳐 실행되더라도 동일 구성의 `POD`는 하나의 서비스가 관리한다.
- 여러 `Worker Node` 간의 분배는 실제 `Load Balancer` 또는 `Ingress`가 담당하는데, 이들은 `Master Node`도 `Worker Node`도 아닌 별도의 `Node`에서 구동되거나 물리적인 전용 하드웨어이다.
- `Replica Sets`는 `POD`의 수를 관리한다.
- 장애 등의 이유로 `POD`가 종료되거나 정의 파일에 정의된 `POD`의 수가 감소하면 실제로 `POD`의 수를 조정한다.
- `Replica Sets`가 관리하는 동일한 구성의 `POD`를 `Replica`라고 한다.
- 그러므로 `POD`의 수를 조정하는 것을 `Replica`의 수를 조정한다고 한다.
- `Replica Sets`는 단독으로 쓰이는 경우가 드물고 `Deployment`와 함께 쓰일 때가 많다.
- `Deployment`는 `POD`가 사용하는 `Image` 등 `POD`에 대한 정보를 갖고 있다.
- `POD`, 서비스, `Replica Sets`, `Deployment` 등을 `Resource`라고 한다.
- `Resource`는 모두 50 여 종류가 있는데 외울 필요 없이 필요에 의해 학습하면 된다.


### `Object`와 `Instance`
- `Resource`가 `etcd`에 등록된 상태에서는 '`○○ Object`' 형태로 관리된다. 
- 그래서 `Resource`를 `Object`라고도 한다.
- `Kubernetes`는 `etcd`에 등록된 내용을 따라 실제 `POD`나 `Service`를 생성하는데, 이때 실제로 생성된 것을 `Instance`라고 한다.


### 서버 한 대로도 `Kubernetes`를 사용할 수 있을까?
- 소규모 서비스라고 `Kubernetes`가 불필요하지는 않다.
- `POD`가 장애 등으로 망가질 경우 자동으로 `POD`를 생성해 대체하므로 관리가 편해진다.
- 특히 `Cloud` 환경에서는 관리 요소가 줄어 들어 이런 장점이 극대화 된다.


### `Kubernetes` 학습 환경
- `Kubernetes`는 본래 대규모 시스템이 전제 조건이다. 
- 따라서 `Master Node`와 `Worker Node`도 별도의 물리적 컴퓨터로 설정하지만, `Docker Desktop`에 내장된 `Kubernetes`에는 컴퓨터 한 대에 `Master Node`와 `Worker Node`를 모두 구축한다. 
- 학습 환경에서는 이를 사용한다.
- 하지만 `Desktop` 내장 `Kubernetes`를 사용해 명령어나 정의 파일 작성 연습은 가능하지만 실제 환경과 차이가 크기 때문에 `Kubernetes`를 모두 익히기 어렵다.
- `Kubernetes`는 컴퓨터의 `Resource`를 소모하므로 사용하지 않을 때는 평소 작업에 지장을 줄 수 있기 때문에 사용하지 않으면 `Kubernetes`를 비활성화 해야 한다.


### `Manifest File`
- `Kubernetes`는 `Manifest File`에 기재된 내용에 따라 `POD`를 생성한다.
- `Manifest File`의 내용을 `Kubernetes`에 업로드하면 `etcd`에 '바람직한 상태'로 등록되어 서버 환경을 이 상태로 유지한다.
- `Manifest File`은 `YAML` 또는 `JSON` 형식으로 기재한다.
- `JSON` 형식은 컴퓨터로 처리하는 것이 목적이므로 사람이 설정 파일을 읽고 쓴다면 `YAML` 파일을 주로 사용한다.
- `Manifest File`명은 따로 정해져 있지 않지만 다른 사람도 이해할 수 있는 이름으로 짓도록 하자.


### `Manifest File`은 `Resource` 단위로 작성한다.
- `POD` 항목은 `Kubernetes` 최대 장점인 자동으로 설정된 개수를 유지하는 기능이 없고 정말 `POD`만을 만들 때 사용하는 항목이므로 작성하지 않는다.
- `POD`는 `Replica Sets`와 `Deployment`를 통해 관리되는데, `Replica Sets` 역시 `Deployment`에 의해 개수가 관리되므로 작성하지 않는다.
- 요약하자면 `Deployment` 항목으로 `Replica Sets`와 `POD`를 관리한다.
- `Manifest File`은 `Resource` 단위로 분할해 작성해도 되고, 한 파일에 합쳐 작성해도 된다.
- 한 파일로 작성할 때는 각 `Resource`를 `---`로 구분한다.


### `Manifest File` 작성
- 주 항목에는 `API` 그룹 및 유형 등의 `Resource` 설정과 `Meta Data`, `Spec`을 작성한다.
- `POD`나 `Service` 같은 `Resource`에 원하는 `Label`을 붙일 수도 있다. 
- `Label`을 부여하면 `Selector` 기능을 사용해 특정 `Label`이 부여된 `POD`만을 배포할 수 있다.


### `POD` 속의 `Volume`
- `POD` 속의 `Volume`은 `POD` 밖에서는 접근할 수 없으므로 `Container` 간의 데이터 공유를 위해 주로 사용된다.
- 예를 들어 주 프로그램이 들어 있는 `Container`와 `Log Monitoring Program`이 들어있는 `Container`를 하나의 `POD`로 구성하여 `Log`를 출력할 `Volume`을 만들어 이 `Volume`을 공유하게 하면 두 `Container`가 `Log` 정보를 공유할 수 있다.


### `POD`의 `Manifest File` 작성
- 실제로는 `POD`의 `Manifest File`을 작성하는 경우는 드물다.
```bash
apiVersion: v1
kind: Pod
metadata:
  name: 컨테이너명
  labels:
    app: 레이블명
spec:
  containers:
    - name: 컨테이너명
      image: httpd
      ports:
        - containerPort: 80
```


### `Deployment`의 `Manifest File` 작성
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: 컨테이너명
spec:
  selector:
    matchLabels:
      app: 레이블명
  replicas: 3
  template:
    metadata:
      labels:
        app: 레이블명
    spec:
      containers:
        - name: 컨테이너명
          image: 이미지명
          ports:
            - containerPort: 80
```


### `Service`의 `Manifest File` 작성
```bash
apiVersion: v1
kind: Service
metadata:
  name: 컨테이너명
spec:
  type: NodePort
  ports: 
    - port: 8099
      targetPort: 80
      protocol: TCP
      nodePort: 30080
  selector:
    app: 레이블명
```
- `Deployment`에서는 `Selector`에 `matchLabels` 항목을 사용하지만 `Service`에서는 사용하면 안 된다.
- `Deployment`의 `matchLabels` 항목은 해당 조건의 부합할 경우를 포함하지만 내부 동작이 다르기 때문에 `Service`에서는 직접 `Resource`를 지정한다.


### `Kubernetes` 명령어

```bash
kubectl 커맨드 옵션
```
- 일반적인 형식은 위와 같다.
- 명령을 하나 하나 실행하며 반영하는 `Docker`와 달리 `Kubernetes`는 `Manifest File` 내용에 따라 한 번에 모든 `Resource`를 생성한다.
- 즉 '바람직한 상태'를 `Kubernetes`가 직접 관리하기 때문에 수작업으로 명령어를 입력할 일은 드물다.


### `Manifest File`로 `POD`/`Service` 생성 실습
#### ✅ `POD` 생성

```bash
kubectl apply -f 파일경로명
```

#### ✅ 생성 여부 확인
- `Deployment`의 `Manifest File`로 생성되는 것은 `POD`이므로 직접 접근해 동작을 확인할 수 없다. 
- 이것이 가능한 것은 `Service`부터다.
- 대신 다음과 같이 `POD`/`Service`의 목록을 확인할 수 있다.

```bash
# POD 목록 확인
kubectl get pods

# Service 목록 확인
kubectl get services
```

#### ✅ 정리

```bash
# Deployment 삭제
kubectl delete -f 파일경로명

# Deployment 목록 확인
kubectl get deployment

# Service 삭제
kubectl delete -f 파일경로명

# Service 목록 확인
kubectl get service
```


### 회고
- 책을 끝까지 읽어야겠다는 생각으로 끝까지 정리해보았지만, `Kubernetes`는 사실 실습을 하면서도 내용이 크게 와닿지 않았는데, 추후 프로젝트에 적용하면서 공부하는 시간이 필요할 것 같다.