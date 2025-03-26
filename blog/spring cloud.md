## 개요

### Spring Cloud란?

- 마이크로서비스 아키텍처(MSA)를 지원하기 위해 다양한 도구와 라이브러리를 제공하는 프레임워크
- 분산 시스템과 클라우드 환경에서 애플리케이션을 효과적으로 개발하고 운영할 수 있는 기능을 제공
- VMWare, 하시코프(HashiCorp), 넷플릭스 등 오픈 소스 회사의 제품을 전달 패턴으로 모아 놓은 도구들의 집합

<br/>

### Netflix OSS(**Open Source Software)**

- Netflix에서 개발한 오픈소스 소프트웨어들의 집합
- 클라우드 네이티브 애플리케이션을 만들기 위한 다양한 도구들을 자체적으로 사용 및 검증하여 라이브러리로 제공함
- 대표적인 도구로는 Eureka, Hystrix, Ribbon, Zuul 등이 존재

<br/>

### Spring Cloud의 특징

1. API 게이트웨이(API Gateway)

   서비스 간의 통신을 중앙에서 관리하는 서버. 주로 라우팅, 인증, 보안, 로드 밸런싱, 캐싱 등의 기능을 담당한다.

   > **- Zuul:** Netflix OSS 기반의 API 게이트웨이. 요청 라우팅, 필터링, 보안 등의 기능을 제공한다.
   > <br/> > **- Cloud Gateway:** 스프링 클라우드에서 제공하는 API 게이트웨이. 비동기 및 논블로킹 방식으로 동작하여 Zuul 보다 더 높은 성능을 제공한다.

2. 서비스 디스커버리(Service Discovery)

   서비스들의 위치와 상태를 자동으로 찾고 관리하는 기능. 서비스는 자동으로 디스커버리 시스템에 등록되고 종료되면 자동으로 해제된다. client는 이를 통해 동적으로 사용 가능한 서비스를 검색하고 선택한다.

   > **- Eureka:** Netflix OSS 기반의 서비스 디스커버리 도구. 서비스 인스턴스가 실행될 때 자동으로 등록되며, 클라이언트는 Eureka 서버를 통해 서비스의 위치를 조회할 수 있다.

3. 분산 설정(Distributed Configuration)

   설정을 중앙에서 관리하고 업데이트하는 기능. 서비스는 해당 서버에서 설정을 동적으로 가져와 사용하며, 이는 서비스를 중단하지 않고 실행 중일 때 변경해도 즉시 적용할 수 있도록 한다.

   > **- Spring Cloud Config:** 중앙 집중식 구성 관리를 제공하는 프레임워크. Spring Cloud Bus를 사용하면 서비스 재시작 없이도 설정 반영이 가능하다.

4. 분산 추적(Distributed Tracing)

   트랜잭션 추적을 위한 기능. Spring Cloud Sleuth를 통해 발생한 작업을 기록하여 마이크로서비스 간의 요청과 응답을 추적하고 모니터링하는 데 사용한다.

   > **- Zipkin:** 각 요청에 고유한 추적 ID를 부여하여 요청의 흐름을 시각화할 수 있다. 요청의 시작부터 끝까지의 경로를 추적하여 지연 시간, 오류 등의 정보를 수집한다. 또한 특정 요청을 검색하고 필터링하여 문제를 진단할 수 있다.

5. 로드 밸런싱(Load Balancing)

   여러 서버에게 들어오는 트래픽을 균등하게 분배하여 부하를 분산. 세션 유지, scale out 과 같은 기능을 통해 네트워크 트래픽을 효과적으로 관리한다.

   > **- Ribbon:** Eureka와 연동하여 서비스 인스턴스 목록을 가져와 동적으로 로드 밸런싱을 수행할 수 있다. 클라이언트는 Ribbon을 통해 여러 개의 인스턴스 중 하나를 선택하여 요청을 보내는 방식으로, 분산 전략으로 라운드 로빈, 가중치 기반 등 다양한 알고리즘을 지원한다.

6. 회로 차단기(Circuit Breaker)

   서비스 간의 통신에서 발생할 수 있는 장애에 대응하는 메커니즘. 서비스 간 통신이 실패할 경우 일시적으로 연결을 차단하여 전체 시스템에 영향을 최소화하고, 일정 시간 이후에 재시도하거나 다른 대체 서비스를 호출한다.

   > **- Hystrix:** Netflix OSS 기반의 서킷 브레이커 라이브러리
   > <br /> > **- Resilience4j:** Hystrix의 대체 도구로, 경량화되고 모듈화된 자바 기반의 서킷 브레이커 라이브러리. 특정 임계치를 초과한 요청을 차단하고, 일정 시간이 지나면 다시 정상 동작을 시도할 수 있다. 실패 시 Failback 매커니즘을 통해 대체 동작을 수행한다.

<br/>

### Spring Boot와의 차이점

- Spring Boot와 Spring Cloud는 모두 Spring Framework 기반으로 개발이 되었지만 각각의 목적과 기능의 차이가 있음
- Spring Boot: ‘단일 애플리케이션 개발’을 위한 프레임워크
  Spring Cloud: ‘분산 시스템의 개발 및 운영’을 위한 프레임워크
- Spring Cloud는 Spring Boot에서 제공하는 기능을 기반으로 분산 시스템에서 필요한 다양한 기능들을 추가로 제공함

⇒ Spring Cloud라는 환경하에 각각의 단일 애플리케이션은 Spring Boot를 이용하여 개발

<br/>

---

## Config server

### Spring Cloud Config

- 개념
  - 분산된 환경에서 중앙 집중식 설정 관리를 제공
- config 서버/클라이언트
  - server: 중앙에서 설정 파일을 관리하고 각 서비스에 제공하는 역할
  - client: config 서버에서 설정을 받아 사용하는 서비스
- 설정 자동 갱신
  - 설정 변경 시 서버 재시작 없이 **실시간**으로 변경 사항을 반영 가능
  - 이를 통해 시스템의 **중단 없이** 즉시 변경 사항 적용 가능
- config 동작 로직

---

## Service Discovery

### Eureka

- 개념
  - 클라이언트가 서비스 인스턴스를 **동적으로 찾을 수 있도록** 지원
  - **서비스의 등록과 검색을 자동화**하여 애플리케이션의 복잡성을 줄여줌
- 서비스 레지스트리 (Service Registry)
  - Eureka는 서비스 레지스트리로 작동하여 모든 서비스 인스턴스의 위치를 중앙 저장소에 저장함
  - 각 서비스는 Eureka 서버에 자신의 위치(호스트 및 포트 정보)를 등록하고, 이러한 정보를 조회하여 통신할 수 있음
- 헬스 체크(Health Check)
  - 서비스 인스턴스의 상태를 주기적으로 확인하는 헬스 체크 기능을 제공
  - 서비스 인스턴스는 주기적으로 자신을 등록한 Eureka 서버에 헬스 체크 정보를 보내며, Eureka는 이를 기반으로 서비스의 가용성을 판단함
- 서비스 등록/조회/서비스 간 호출 로직

<br/>

### 구현

- application.yml

  ```yaml
  ### Eureka 서버 설정(서버 역할을 할 경우) ###
  server:
  	port: 8761  # Eureka 서버가 실행될 포트 (클라이언트에는 필요 없음)

  # Spring Boot 애플리케이션 이름 설정
  spring:
    application:
      name: my-service  # Eureka에 등록될 서비스 이름
    profiles:  # profile이란? dev, prod 등
  	  active: default
    cloud:
  	  config:
  		  label: "discovery"  # Config Server에서 가져올 레이블 설정
  		  enable: false  # Spring Cloud Config 사용 여부 (false이므로 Config Server 사용 안 함)
  		  fail-test: false  # Config Server 연결 실패 시 테스트 여부
    config:
  	  import: configserver:${configServerURI}


  eureka:
  	### Eureka 서버 관련 설정 ###
  	server:
  		enable-self-preservation: true  # 자기 보호 모드 활성화
  		response-cache-update-interval-ms: 5000  # 5초마다 응답 캐시를 갱신
  		activation-interval-timer-in-ms: 1000  # 1초마다 활성화 여부 확인
  	### Eureka 클라이언트 관련 설정 ###
  	client:
      register-with-eureka: true  # Eureka 서버에 이 서비스 등록 여부 (기본값: true)
      fetch-registry: false  # Eureka 서버에서 다른 서비스 목록을 가져오지 않음 (기본값: true)
  	  registry-fetch-interval-seconds: 2  # Eureka 서버에서 서비스 정보를 가져오는 주기 (2초).
      service-url:
        defaultZone: http://localhost:8761/eureka/  # Eureka 서버의 기본 주소
       ### Eureka 인스턴스 설정 ###
      instance:  # 인스턴스란 서버 또는 컨테이너 단위
  	    prefer-ip-address: true  # 호스트명 대신 IP 주소를 사용해 Eureka에 등록
  	    ip-address: {hostIP}  # 환경변수로 IP 지정

  ### 로그 설정 ###
  logging:
  	config: classpath:logger.xml  # 로그 설정을 logger.xml 파일에서 로드
  ```

---

## Gateway Server

### Zuul

- 개념
  - 넷플릭스가 개발한 API 게이트웨이로, 모든 서비스 요청을 중앙에서 관리
- 특징
  - 라우팅: client의 요청 url에 따라 적절한 백엔드 서비스로 요청 전달
  - 필터: 요청 전후 다양한 작업을 수행할 수 있는 필터 체인을 제공. 인증/권한 부여/로깅 등을 처리
  - 모니터링: 요청 로그 및 메트릭을 통해 서비스의 상태를 모니터링 가능
- 라우팅 동작 로직

## <br />

### References

- [[Java] Spring Cloud 이해하기 -1 : 주요 특징으로 이해하기](https://adjh54.tistory.com/207)
- [Spring Cloud란 무엇인가](https://velog.io/@zo_meong/Spring-Cloud%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
- [[Spring Cloud] Spring Cloud란 무엇일까 (마이크로서비스 ...](https://pixx.tistory.com/273)
