# kube-monkey 사용 방법

[Forked repository from asobti/kube-monkey](https://github.com/anencore94/kube-monkey/tree/v1)

> kube-monkey 사용 방법을 간략하게 정리해둔 문서입니다. 지속적으로 업데이트될 예정입니다.

### 개요

- upstream repository 의 [README.md](https://github.com/asobti/kube-monkey) 에도 쓰여있다시피 Netflix's Chaos Monkey 의 k8s 버전 구현체입니다.
    - Deployment, DaemonSet, ReplicaSet 리소스를 List 한 뒤, 각 리소스 내부의 pod 목록을 조회 후,
    - BlackList(WhiteList) 체크, label 체크 등을 진행하여
    - 1차적으로 kube-monkey 가 kill(delete 요청) 할 pod 목록을 뽑고,
    - 이렇게 지정된 희생자 pod 들을 확률적으로 kill 할 지 말 지 결정한 다음
    - 최종적으로 정해진 희생자 pod 들을 언제 죽일지 스케줄링하고,
    - 스케줄된 시간에 실제로 podDelete 요청을 날립니다.
- 한 마디로, 특정 k8s cluster 내에 chaos monkey 를 풀어놓고 휘젓게 시킨 다음, 그럼에도 k8s cluster 가 잘 동작하는지를 테스트하기 위한 프로젝트입니다.
    - pod 자체를 delete 하지 않고, deployment, daemonset, replicaset 등 replica 가 설정된 pod 만 delete 하여 replciation 이 잘 되는지를 테스트하는 것으로 보입니다.


### 빌드 및 배포 방식

> helm chart 를 이용한 deploy 방식도 제공하는데, 제가 잘못 사용한 것인지 제가 설치했을 때는 정상적으로 설치되지 않아 manual 방식으로 deploy 하였습니다.

- kube-monkey 의 빌드 및 배포 방식은 다음과 같습니다.
    - 우선 `make build` 명령을 통해 하나의 실행파일을 생성한 다음,
    - 'make container` 명령을 통해 이 실행파일을 실행하도록 docker image 파일이 local 에 생성합니다.
    - 이제 이 image 파일을 바라보도록 examples/deployment.yaml 의 registry, tag 등을 설정한다음 배포하면, kube-monkey 가 하나의 pod 으로 k8s cluster 환경에 뜨게 됩니다.
        - **이 때, kube-monkey deployment(pod) 이 정상 생성되기 위해서는 먼저 examples/debug-mode-configmap.yaml 과 같은 환경 변수들을 설정해야 합니다.**
            - 각각의 parameter 에 대한 설명은 다음 코드의 주석에 잘 설명되어있습니다. [param 링크](https://github.com/asobti/kube-monkey/blob/master/config/param/param.go)
        - 혹시 이 configmap 을 배포하기 전에 deployment 를 배포하면 deployment 가 정상 생성되지 않을텐데, 그냥 deloyment 삭제 -> configmap 배포 -> deplolyment 생성 하면 잘 생성됩니다.
    - kube-monkey 배포 후 사용하고 계시는 cluster 환경에 따라 다음과 같은 작업이 필요할 수 있습니다. [관련된 stackoverflow 질문](https://stackoverflow.com/questions/47973570/kubernetes-log-user-systemserviceaccountdefaultdefault-cannot-get-services)

        ```{shell}
        # 아래와 같은 문제가 발생한다면
          User "system:serviceaccount:default:default" cannot get services in the namespace 

        # 아래와 같은 작업이 필요함
          kubectl create clusterrolebinding serviceaccounts-cluster-admin \
          --clusterrole=cluster-admin \
          --group=system:serviceaccounts
        ```

### **asobti github 수정 pr 필요한 부분**

- examples/deployment.yaml 의 ApiVersion 을 apps/v1 으로 변경 혹은 새로 추가
    - `selector:` section 추가 필요
- README.md 에 빌드 방법 `make container` 만 하면 된다고 쓰여있는데,
    - `make build && make continaer` 하여 이미지 빌드 후에 tag 붙이고 private repository 에 push 한 후 deployement.yaml 의 repository url 변경해야한다고 수정 필요

### 코드 분석
- TODO)

### 개인 작업용 (스크립트화 필요)
```
1. 코드 수정
2. VERSION 수정
3. kubectl delete -f examples/deployment.yaml
4. make build && sudo make container
5. sudo docker tag kube-monkey:myv1.0.1 localhost:5000/kube-monkey:myv1.0.1 && sudo docker push localhost:5000/kube-monkey:myv1.0.1
    - 태그(myv1.0.1) 수정
6. examples/deployment.yaml 에서 image tag 수정
7. kubectl apply -f examples/deployment.yaml
8. kubectl get po 해서 kube-monkey-XXXXX 확인
9. kubectl logs kube-monkey-XXXXX
```

### 개인 저장용 (label 붙이는 커맨드)
> ~~pod 에는 identifier 라벨만,  deployment 에는 모든 라벨 추가 필요 (deployment 에 label 이 있는지 먼저 확인 후 그 안의 pod 들에 label 이 있는지 확인하는 프로세스)
(deployment 를 create 하는 yaml에 identifier 관련해서 적지 않고 생성된 deployment, pod 에 patch 로 추가할 경우는 한번 지운 pod 은 label 이 사라진 위의 yaml 버전으로 재생성되어 더 이상 절대 삭제되지 않음)
(=> 현재, cdi, rook-ceph 같은 경우 모두 deployment 형태로 배포하지 않고 deployment 내부에서 각 pod 의 이미 있는 이미지들로 만들어버려서 이런 이미지들로 띄우는 deployment resource 들은 label 설정이 되지 않음 => 방법이 있는지?)~~

```
# pod (cdi-deployment-5bf7945884-n6k6z) 에는 kube-monkey/identifier 라벨만 추가

kubectl -n cdi label pods cdi-deployment-5bf7945884-n6k6z kube-monkey/identifier=monkey-victim
```

```
# deployment (cdi-deployment) 에는 4개의 label 모두 추가

kubectl -n cdi label deployments cdi-deployment kube-monkey/enabled=enabled && kubectl -n cdi label deployments cdi-uploadproxy kube-monkey/identifier=monkey-victim && kubectl -n cdi label deployments cdi-uploadproxy kube-monkey/kill-mode=fixed && kubectl -n cdi label deployments cdi-uploadproxy kube-monkey/kill-value=1 && kubectl -n cdi label deployments cdi-uploadproxy kube-monkey/mtbf=2
```
