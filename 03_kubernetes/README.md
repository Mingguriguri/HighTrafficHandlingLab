# 쿠버네티스 실습

- 키워드: Kubernetes
- Ref.: [그림과 실습으로 배우는 도커&쿠버네티스 (저자: 오가사와라시게타카)](https://www.yes24.com/Product/Goods/108431011)
- 날짜: 2024/03/04
- 주제: 컨테이너


>📌 **실습한 내용을 정리한 문서로, 이론적인 부분은 없습니다.**

# 🔍도커 데스크톱의 쿠버네티스 준비

## Step 1: 쿠버네티스 활성화
도커 데스크탑 - 설정
![Untitled](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/156d355f-8bb1-4a00-a02d-b5dae0db5779)


## Step 2: 쿠버네티스 클러스터 설치
![Untitled 1](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/2099a20d-67f0-42b7-82a9-b2630e9b2b5e)


## Step 3: 준비 완료

Before
![Untitled 2](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/3f89d0c9-c4c3-485f-9e08-4f4301d7a8a4)

After
![Untitled 3](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/d6ee885c-500d-4bea-a076-67ecb03b3631)


## 번외: 쿠버네티스 삭제 또는 비활성화

- 쿠버네티스는 컴퓨터의 리소스를 소모한다.
- 따라서 사용하지 않을 때는 평소 작업에 지장을 줄 수 있다.
- 이런 경우에는 [Enable Kubernetes] 항목을 체크해제하여 쿠버네티스를 비활성화한다.
- 또 쿠버네티스 클러스터를 초기화하려면 [Reset Kubernetes Cluster] 버튼을 클릭하면 된다.
![Untitled](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/9bd1b7e6-14ea-4171-85dc-25dcb16c58be)


# 메니페스트 파일(정의 파일) 작성

### 파드

**`myfirstk8spod.yml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myfirstk8spod # pod name
  labels:
    app: myfirstk8skube
spec:
  containers:
  - name: myfirstk8sex91 #container name
    image: httpd
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
    ports:
      - containerPort: 80
```

### 디플로이먼트

**`myfirstk8sdep.yml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myfirstk8sdep
spec:
  selector:
    matchLabels:
      app: myfirstk8skube
  replicas: 3 # 파드 수를 몇 개로 유지할 것인지.
  template: # 생성할 파드의 정보
    metadata:
      labels:
        app: myfirstk8skube
    spec:
      containers:
      - name: myfirstk8sex91
        image: httpd
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80
```

### 서비스

**`myfirstk8sdep.yml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myfirstk8sser
spec:
  type: NodePort # 서비스 유형
  ports: # 포트설정
  - port: 8099 # 서비스의 포트
    targetPort: 80 #컨테이너의 포트
    protocol: TCP # 통신에 사용되는 프로토콜
    nodePort: 30080 #워커노드의 포트
  selector:
    app: myfirstk8skube
```

# 쿠버네티스 명령어

> create, edit, delete, get, set, apply, describe, diff, expose, scale, autoscale, rollout, exec, run, attach, cp, logs, cluster-info, top


## 🔍매니페스트 파일로 파드 생성(1) - 디플로이먼트

### 1. 디플로이먼트의 매니페스트 파일 읽어들이기

> **매니페스트 파일을 읽어 실제 리소스에 반영 ⇒ 파드의 생성 여부 확인**

```yaml
kubectl apply -f .\myfirstk8sdep.yml
```
![Untitled 4](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/d68fd46e-b176-4602-87ab-a3a35c948ca2)


[[K8S] 매니페스트 파일 이름 제약](https://www.notion.so/K8S-fa30ce76ee1b47978b0fda3a67ceb107?pvs=21) 
![Untitled 5](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/344ee3f1-2906-4e0b-ad6b-2a4580ce4a55)


### 2. 파드가 잘 생성됐는지 확인

```powershell
kubectl get pods
```
<img width="960" alt="Untitled 6" src="https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/7fe825ad-bed2-49ec-8c0d-dbff2b1dc581">


## 🔍매니페스트 파일로 파드 생성(2) - 서비스

> **매니페스트 파일을 읽어 실제 리소스에 반영 ⇒ 파드의 생성 여부 확인 ⇒ 파드에 접근 가능한지 확인**
> 

### 1. 서비스의 매니페스트 파일을 읽어 들이기

```powershell
kubectl apply .\myfirstk8sser.yml
```

### 2. 서비스가 잘 생성됐는지 확인

```powershell
kubectl get services
```
![Untitled 7](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/b357b988-7560-40fc-8332-a4c3a81babaf)


### 3. 동작 확인
![Untitled 8](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/99f17eb1-da12-4129-91d8-2f2a7f3983d1)


# 쿠버네티스 연습하기
![Untitled 9](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/8124beec-62fb-44c9-8c79-eed4fac909d6)

![Untitled 10](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/a8cf0ba8-9fc4-4106-99e1-16dafa8aef48)
![Untitled 11](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/6c921e68-d21a-405a-aa6f-b1bb726f846d)

## 🔍매니페스트 파일로 파드 개수 늘리기

- 쿠버네티스는 매니페스트 파일을 데이터베이스(`etcd`) 에서 읽어들이고, 이렇게 등록된 ‘바람직한 상태’를 유지하는 기능을 한다.
- ‘바람직한 상태’는 매니페스트 파일을 다시 읽어들이면 덮어씌워지므로 매니페스트 파일을 수정해 다시 읽어 들여 파드의 수나 종류, 상태를 변화시킬 수 있다.
- 레플리카 수를 수정해보도록 한다.

> **레플리카 수 변경 ⇒ 매니페스트 파일을 다시 읽어 리소스에 반영 ⇒ 파드가 생성됐는지 확인**

### 1. 디플로이먼트의 매니페스트 파일 수정

**`myfirstk8sdep.yml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myfirstk8sdep
spec:
  selector:
    matchLabels:
      app: myfirstk8skube
  replicas: 10 # 파드 개수 늘리기 3->10
  template:
    metadata:
      labels:
        app: myfirstk8skube
    spec:
      containers:
      - name: myfirstk8sex91
        image: httpd
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80

```
![Untitled 12](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/a0654dc3-ec39-4157-8788-a5f2ce46de41)


### 2. 매니페스트 파일 읽어들이기

수정한 매니페스트 파일을 쿠버네티스에 읽어 들이고 그 내용을 리소스에 반영한다.

```powershell
kubectl apply -f C:\Users\rlaal\HighTrafficHandlingLab\03_kubernetes\myfirstk8sdep.yml
```
![Untitled 13](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/ae84a7ea-5e96-416c-813c-1d4e448ed72e)


### 3. 파드의 수가 늘어났는지 확인하기

```powershell
kubectl get pods
```
![Untitled 14](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/42ab0968-1c1c-4e28-bf7e-05fcaafa1884)


10개로 늘어난 것을 볼 수 있다. 

좀 더 늘려보자

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myfirstk8sdep
spec:
  selector:
    matchLabels:
      app: myfirstk8skube
  replicas: 30 # 파드 개수 늘리기 3->10->30
  template:
    metadata:
      labels:
        app: myfirstk8skube
    spec:
      containers:
      - name: myfirstk8sex91
        image: httpd
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80

```
![Untitled 15](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/ab8c2cfd-d324-4584-849b-3cb0bb502693)

![Untitled 16](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/fbd4e2b7-a7c6-4c0b-81a7-627d7c208364)

![Untitled 17](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/5d1cb45c-2531-4de2-a85a-6a29ed8799e1)



## 🔍매니페스트 파일로 아파치를 nginx로 바꾸기

- 파드의 수 말고도 컨테이너의 종류도 바꿀 수 있다.
- 지금까지는 아파치 컨테이너를 생성했으나, 이를 nginx 컨테이너로 바꾼다.
- 사용한 파일을 그대로 사용하지만 이미지 이름을 httpd에서 nginx로 수정만 하면 된다.

> **이미지 종류 수정 ⇒ 매니페스트 파일을 다시 읽어 리소스에 반영 ⇒ nginx 컨테이너로 바뀌었는지 확인**


### 1. 디플로이먼트의 매니페스트 파일 수정

**`myfirstk8sdep.yml`**

before

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myfirstk8sdep
spec:
  selector:
    matchLabels:
      app: myfirstk8skube
  replicas: 30 # 파드 개수 늘리기 3->10->30
  template:
    metadata:
      labels:
        app: myfirstk8skube
    spec:
      containers:
      - name: myfirstk8sex91
        image: httpd
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80

```


after

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myfirstk8sdep
spec:
  selector:
    matchLabels:
      app: myfirstk8skube
  replicas: 30 # Increase the number of pods (3->10->30)
  template:
    metadata:
      labels:
        app: myfirstk8skube
    spec:
      containers:
      - name: myfirstk8sex91
        image: nginx # Change to Nginx Container (httpd -> Nginx)
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
        ports:
        - containerPort: 80

```

![Untitled 18](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/b9aa5129-6c70-4759-80e4-7520823850f3)

### 2. 매니페스트 파일 읽어들이기

```yaml
kubectl apply -f (파일경로).\myfirstk8sdep.yml
```
![Untitled 19](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/900710e3-9fcb-4d09-9a7e-d805c50c4195)


### 3. 동작 확인

[[K8S] 매니페스트파일 - 컨테이너 이미지 적용 안됨(리소스 제한)](https://www.notion.so/K8S-2541f9ade8354524b07fec20f69f349d?pvs=21) 
![Untitled 20](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/625caa48-b4ad-44a7-8fbe-88900e85691b)


## 🔍수동으로 파드를 삭제한 후 자동복구되는지 확인

- 쿠버네티스는 ‘바람직한 상태’를 유지하려고 한다. 이 유지 기능을 시험해보자.
- 수동으로 파드 하나를 삭제한 후, 코버네티스가 파드의 수를 유지하기 위해 자동으로 파드를 생성하는지 확인한다.

> **명령어로 파드를 하나 삭제 ⇒ 파드가 삭제됐는지 확인 ⇒ 새로운 파드가 보충됐는지 확인**
> 

### 1. get 커맨드로 파드의 목록 확인

```powershell
kubectl get pods
```
![Untitled 21](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/cc79e18c-d5dc-471b-8cdb-18090906fbb4)


맨 위의 파드 ID 사용

### 2. 수동으로 delete 커맨드를 실행해 파드를 하나 삭제

```powershell
kubectl delete pod myfirstk8sdep-58d47ddc68-5tn8j
```
![Untitled 22](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/a7460f90-43ed-4a28-a3cb-cfd4c1477442)


### 3. 삭제된 파드가 다시 보충되는지 확인

```powershell
kube get pods
```

AGE에서 가장 최근 목록을 통해 알 수 있다. 다른 ID 파드가 보충되었다.
![Untitled 23](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/c61baf74-3f12-49b3-9265-ccf0d1e935c2)


## 🔍생성했던 디플로이먼트와 서비스 삭제

- 파드는 레플리카 수를 0으로 수를 0으로 수정하면 모두 삭제되지만, 이것만으로는 디플로이먼트와 서비스가 남아있게 된다.
- `delete` 명령어로 삭제하도록 한다.

> **디플로이먼트 삭제 ⇒ 삭제확인 ⇒ 서비스 삭제 ⇒ 서비스 확인**


### 1. delete 커맨드로 디플로이먼트 삭제

```powershell
kubectl delete -f .\myfirstk8sdep.yml
```

### 2. 디플로이먼트의 삭제 확인

```powershell
kubectl get deployment
```
![Untitled 24](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/b87b19fa-1fcf-4fe9-98a5-c673cf179f97)


### 3. delete 커맨드로 서비스 삭제

```powershell
kubectl delete -f .\myfirstk8sser.yml
```

### 4. 서비스 삭제 확인

```powershell
kubectl get service
```
![Untitled 25](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/39b3df7b-01a8-43c4-aeb4-d3a7dda10f64)


로컬호스트도 연결끊김
![Untitled 26](https://github.com/Mingguriguri/HighTrafficHandlingLab/assets/101111603/c2746695-c136-4668-8a77-7c684a8d8018)

