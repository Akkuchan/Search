# MSA(Microservice Architecure)
- 마이크로 서비스란? 
	- API를 기반으로 통신하는 소규모의 독립적으로 서비스로 배포 가능한 기능을 수행하는 서비스로 구성된 프레임워크이다. 
	- MSA는 애플리케이션의 확장에 용이하고 빠른 속도의 개발과 관리에 용이하다. 
	- 빠르게 변화하는 IT 시장에 적응하는데 유용한 개발 아키텍쳐중 하나이다. 
	- MSA가 무엇인지 본격적으로 살펴보기에 앞서 이와 비교되는 개념부터 살펴보자

## Monolithic
- 애플리케이션의 모든 프로세스가 긴밀하게 결합되어 하나의 프로젝트로 통합되어 있는 형태의 아키텍쳐이다. 개발에 필요한 부분을 모듈별로 개발하고 이를 하나의 그룹으로 통합한 결과물을 만들어 패키지로 배포하는 형태이다.  웹의 겨우 .War의 빌드파일을 WAS로 배포하는 형태이다. 

![[Pasted image 20220811211943.png]]
- 이러한 배포 방식은 모듈들을 모아 한번에 빌드하고 관리하기에 얼핏보면 편리해보일 수 있으나. 여러 패키지, 모듈간의 상호작용이 얽혀있어(즉 종속성이 커져) 유지보수의 어려움이 생길 경우 해결하기 어려울 수 있다.
- 또한 하나의 구조 변화가 전 모듈과 아키텍쳐 계층에 영향을 줄 가능성이 있어 이로 인한 비용의 문제 또한 커질 수 있다. 

## MSA의 특징
- 이러한 문제를 해결하기 위해 MSA는 어플리케이션을 구성하는 각 모듈, 부분을 서비스, 즉 비즈니스 경계에 맞게 세분화하고 각 서비스간에는 네트워크 호출로 통신한다. 
- 이로써 상호간의 종속성은 약화시키되 높은 확장성, 회복성, 유연성을 갖춘 어플리케이션을 구축할 수 있게 된다.
 ![[Pasted image 20220811212624.png]]
- 즉 쉽게 말해 MSA의 가장 큰 특징은 어플리케이션의 각 요소가 서로 분리되어 있다는 것이다. 
- 이러한 분리성이 왜 개발에 있어 유용할까?
- 이는 위에서 언급했던 Monolithic 아키텍처의 단점을 보완하는데 최적의 환경을 제공하기 때문이다. 
	- 이를 자세히 살펴보면 다음의 요소가 있다.
		1. Monolithic대비 새로운 기능 추가 및 업데이트에 용이하다. 수정할 부분만 따로 골라 수정하면 된다.
		2. 특정 부분 발생시 해당 문제가 다른 서비스에 영향을 주게되는데 MSA에서는 API로만 통신하고 서로 분리되어 있기에 문제의 전이를 예발할 수 있다.
		3. Monolithic은 여러 역할을 하는 시스템이 하나의 서버에 함께 올라가 있기에 Scale-Out시 필요없는 자원이 함께 증가된다. 반면 MSA는 Scale-out이 필요한 서비스만 적용하면되기에 불필요한 나머지 서비스의 추가 생성, 즉 중복을 방지할 수 있다는 점에서 유용하다. 
		4. 보다 신속하게 배포와 업데이트를 할 수 있다.
			- Monolithic의 경우 작은 부분만 변경하는 경우에도 전체 애플리케이션을 새로 빌드하여 재배포 해야 한다.  수정 후에 문제가 발생한다면 2번의 문제를 고려해 재수정 및 재빌드, 재배포를 해야한다.  즉 1차 목표인 에러 해결을 위한 코드 수정 이외에 추가로 들어가는 작업이 많아진다.
			- 반면 MSA는 각 서비스가 분리되어 있기에 수정 부분만 수정, 빌드, 배포하면 되기에 절대적인 업무량이 줄어들기 마련이다.

## MSA 아키텍처 이해를 위한 개념들
![[Pasted image 20220811215933.png]]
- MSA를 구성하는 주요 Component(여러 마이크로 서비스들)는 아래와 같은 것들이 있다.
	1. Config Management : 서비스의 재빌드,재부팅 없이 설정사항을 반영(Netflix Archaius, Kubernetes Configmap)
		- 우리가 개발하는 아키텍쳐의 환경변수 등의 설정정보를 담은 이미지를 별도의 객체로서 다루는 서비스
	2. Service discovery: MSA기반 서비스 배포시 서피스 검색 및 등록(Nexflix Eureka, Kubernetes Service, Istio)
		- 각 서비스들은 서로 API를 기반으로 통신하기에 이를 관리하고 필요한 정보들(각 서비스의 위치, 호출방법)을 담은 서비스 객체에 해당한다. 
	3. API Management : 클라이언트 접근 요청을 일원화(Netflix Zuul, Kubernetes Ingress)
		- 외부의 요청이 검증된 것인지를 관리하기 위한 서비스 객체
	4. Centralized Logging : 서비스별 로그의 중앙집중화(ELK Stack)
		- 로그를 따로 관리하는 것이 아닌 한 곳에서 관리하게 해주는 서비스
	5. Dustributed Tracing : 마이크로 서비스 간의 호출 추적(Spring Cloud Seleuth, Zipkin)
		- 서비스간의 호출을 추적하기 위한 서비스 객체
	6. Centralized Monitoring : 서비스별 메트릭 정보의 중앙집중화(Netflix Spector, Heapster)
		- 서비스가 분산되어 있지만 이들이 잘 동작하는지를 점검하고 관리하기 위해서는 해당 기능을 한 곳에 집중화해 효율적으로 이를 수행해야한다. 이를 위한 서비스 객체
	7. Resilience & Fault Tolerance
		- 한 서비스의 장애가 연쇄작용으로 전체 서비스, 어플리케이션의 에러로 전파되지 않도록 하는 기능이 필요함 
			- Ex. 한 서비스의 에러로 쓰레드가 쌓여 과부하가 발생할 수 있기에 이를 방지하기 위한 서킷브레이커와 같은 중재장치를 마련해 방지하는 것
	8. Auto-Scailong & Self-Healing : 자동 스케일링, 복구 자동화를 통한 서비스 관리 효율화(Kubernetes JOs & Scheduled Jobs)

## MSA의 적용 사례
- 배달의 민족 : 기존에 애플리케이션에 포함된 DB를 모두 루비(MS-SQL 단일로)로 관리함. 이로 인해 루비 DB의 문제로 인해 애플리케이션 자체를 이용할 수 었게 되어 이를 방지하고자 MSA 도입
- 11번가 : 기존의 웹이 Monolithic이었기에 기능 개선과 배포에 자원이 많이 소모됨. 이로 인해 성능 저하가 발생하였기에 Netflix OSS와 spring boot 기반의 MSA 아키텍쳐로의 전환을 꾀함


## MSA의 적용시 발생 가능한 문제들
- 마틴 파울러 : "시스템이 그리 복잡하지 않다면 MSA를 지양할 것."
- 시스템 자체의 복잡도가 클 경우에는 MSA를 적용하는 것이 효율적일 것이다. 하지만 중소규모의 프로젝트, 애플리케이션의 경우 Monotholic 만으로도 충분히 작업을 수행하는데 문제가 없다. 때문에 현재 제공하는 서비스에 MSA를 적용하는 것을 너무 섣불리 도입하지 말 것을 권장한다. 
	- Montholic 대비 복잡한 구조로 이루어져 있기에 API를 통한 통신을 연결된 서비스마다 구녛애 주어야 한다. 또한 통신 장애나 내부 에러를 어떻게 처리하고 관리하여 트랜잭션을 유지할지를 고민해야 한다.
- 각 컴포넌트(서비스)들이 떨어져 있기에 개별 테스트 대비 통합 테스트 코드를 구현하는데 어려움이 따른다. 각 서비스마다의 개발 환경도 차이가 있고 이를 통일화하는 작업 또한 복잡할 것이기 때문이다.  
- 실제 운영환경에 대해서 배포하는 것이 쉽지 않다. 마이크로서비스의 경우 서비스 1개를 재배포 한다고 하면 다른 서비스들과의 연계가 정상적으로 이루어지고 있는지도 확인해야한다. 

출처:
https://wooaoe.tistory.com/57](https://wooaoe.tistory.com/57) [개발개발 울었다:티스토리
https://www.youtube.com/watch?v=8d4h7K_Fq-0