# Kubernetes
## Kubernetes?
* Spring Cloud로도 MSA를 구현할 수 있다.
* 하지만 Kubernetes는 오픈소스로 공개된 이후 가장 활발히 커뮤니티가 활성화되어있다.
* 때문에 많은 사람들이 채택하여 활용중이기 때문에 많은 트러블슈팅 사례와 적용 사례들이 있다.
* Kubernetes를 사용하면 다른 서비스들과의 연계도 편하기 때문에 Kubernetes로 MSA를 구현하는 추세임

## Pod
* Kubernetes에서 생성하고 관리할 수 있는 배포가능한 가장 작은 단위이다.
* 하나 이상의 컨테이너를 포함한다.
* 일반적으로 밀접하게 연결된 Application이 올라간 컨테이너들을 Pod로 묶어서 배포한다.
* Deployment 혹은 Job과같은 워크로드를 이용하여 생성한다.
* Pod 생성 시 휘발성 성질은 가진 IP를 자동으로 할당한다.

## Service
* Cluster IP를 통해 유동적으로 생성되고 사라지는 Pod에 접근하기 위한 방법으로 사용된다.
* 여러 Pod를 묶어 적절한 Pod로 트래픽을 라우팅하는 로드 밸런싱 기능을 제공한다.
* 클러스터의 Service CIDR중에 지정된 IP로 생성 가능하다.
* ClusterIP / NodePort / LoadBalancer 타입으로 제공되는데, 기본 옵션은 클러스터 내부에서만 사용할 수 있는 Cluster IP이다.
    * LoadBalancer : 외부에 노출되어 외부의 트래픽을 적절한 Pod로 트래픽을 라우팅
    * NodePort : 포트포워딩 방식
    * ClusterIP : Cluster 내부에서 트래픽을 라우팅

## Ingress
* Service는 L4레이어로 TCP에서 Pod들을 밸런싱하며, URL Path에 따른 서비스간 라우팅이 불가능하다.
* Ingress는 서비스간 Routing을 위해 API Gateway를 사용하는데, L7에서 URL기반 라우팅 기능을 수행한다.
    * 쿠버네티스 HTTP/HTTPS 기반의 L7 로드밸런싱 기능을 제공하는 컴포넌트이다.
* Service 앞에서 L7 로드밸런서 역할을 하고, URL에 따라 라우팅한다.

## ConfigMap, Secrets
* 컨테이너에 들어갈 애플리케이션의 설정 값을 외부에서 제어할 수 있도록 도와주는 오브젝트이다.
* Key : Value 값으로 선언한다.
* 일반적인 정보 값은 ConfigMap으로 생성하고, 민감한 기밀 정보 값은 Secrets로 생성한다.

## Monitoring
* Host : 노드의 CPU, 메모리, 디스크, 네트워크 사용량 등을 모니터링
* 컨테이너 : 컨테이너의 CPU, 메모리, 디스크, 네트워크 사용량 등을 모니터링
* Application : 컨테이너 안에 구동되는 개별 애플리케이션 지표를 모니터링
* Kubernetes : 컨테이너를 컨트롤하는 쿠버네티스 자체에 대한 모니터링