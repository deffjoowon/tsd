# 30장 배포: PaaS
규모가 작은 프로젝트의 경우 PaaS(Platform as a Service)를 이용함으로써 많은 이득을 얻을 수 있다.

> **절대 PaaS에 종속되지 않도록 하자**  
> 매우매우 편리하지만!   
> 급격한 가격인상, 성능 퇴보, 계약사항의 변경, 라이센스 관계 변경, 서비스 자원의 급격한 감소, 서비스 종료와 같은 위험 요소가 있다.  
> 호스팅 제공사를 이전할 때 프로젝트의 주요 구성 자체를 변경하지 않고 이전할 수 있도록 준비가 필요하다. 

WSGI 호환 프레임워크로 장고를 지원하는 가장 일반적인 서비스 업체 

- [허로쿠](http://heroku.com)
- [파이썬애니웨어](https://www.pythonanywhere.com)

## 30.1 PaaS 선택하기 

### 30.1.1 법적 제약 
PaaS를 선택할 때, 해당 시스템이 각 지역별, 국가별 법적 요구 조건을 충족하는지 확인해야한다. 

### 30.1.2 가격
대부분의 PaaS 옵션은 처음 시작하는 사용자 혹은 토이프로젝트에 대해 무료 서비스를 제공한다.  
하지만 프로젝트와 서비스에 대한 주의를 게을리한다면 어마어마한 월 사용 요금을 내야할 수 있다.  
**서비스 비용과 요금 명세표에 늘 주의하자!**  

### 30.1.3 가동시간 
가동시간은 Paas 업체에 매우 까다롭고 민감한 사안이다.  

- 허로쿠, 파이썬애니웨어를 비롯한 PaaS 업체는 대부분 AWS나 랙스페이스(Rackspace) 같은 업체로부터 공간을 빌려 이용한다. = 해당 업체에 문제가 생기면 PaaS 업체도 문제를 겪는다.
- 모든 시설은 물리적 인프라스트럭처에 기반하고 있기에 자연재해나 산업사고로 모든 것이 중지될 수 있다. 

> **매우 높은 수준의 가용성이 필요하다면?**  
> 시스템이 즉각 반응하지 않으면 사람의 목숨이 위험해진다거나... 
> 그렇다면 PaaS는 추천할 만한 방안이 아니다. 

### 30.1.4 PaaS 회사의 인력 배치 
PaaS 회사의 인력배치가 얼마나 높은 수준인가?  
충분한 인력 확보가 되지 않았다면 충분한 지원을 받을 수 없다. 

### 30.1.5 스케일링 
얼마나 쉽게 스케일 업이 가능한가?  
트래픽이 잠깐 동안만 급증하는 경우에 재빨리 원상태로 돌아올 수 있는가?  
이런 프로세스를 자동화할 수 있는가?  

### 30.1.6 문서화 
꾸준히 잘 정리된 문서를 제공하는 서비스인가?  
쉽게 검색 가능한 참고자료를 제공한다는 것은 매우 중요함! 

### 30.1.7 성능 저하 
프로젝트가 갑자기 느려진다면, 다음과 같은 절차로 문제를 짚어보자.

- 성능 저하가 될 만한 변경사항에 대해 커밋 이력을 확인한다. : 큰 버그가 있을 수 있음
- 병목현상을 조사한다.
- PaaS 지원팀에게 질문을 한다
- 물리적 하드웨어의 문제일 수도 있다. : 새로운 프로젝트 인스턴스를 띄우고 데이터를 이전한다. 
- PaaS 지원팀에게 자세한 도움을 요청한다.

전혀 다른 환경에서는 문제가 없다면 시스템을 이전할 떄가 된 것이다. 

> **무료, 초보자용 서비스는 성능이 떨어진다.**


### 30.1.8 지리적 요건 
PaaS 서비스가 위치한 지리적 위치와 서비스가 주로 사용될 장소를 고려하자.
중국 사용자가 주 대상인데 미국에 기반을 PaaS를 써야한다면 좋은 옵션은 아닐 것이다.(네트워크 지연 이슈 등)

### 30.1.9 회사의 안정성
PaaS 서비스는 해결해야할 문제가 상당히 많은 사업이다. 
업체가 베타 기간이나 초기 시작 기간을 지났는데도 이익을 낼 수 없다면, 해당 업체는 위험할 수 있다. 

## 30.2 PaaS로 배포하기 
### 30.2.1 동일한 환경을 목표로 잡는다
**배포의 핵심:** 개발환경과 상용환경을 완전히 똑같이 맞추는 것  

하지만 PaaS를 사용한다면 상용환경에 대한 설정은 컨트롤하기 어렵다.  
가능한한 개발환경과 상용환경을 최대한 동일하게 유지하자. 

**운영환경의 문제를 재현하는 최선의 방법:** 로컬에서 직접 운영환경에 준하는 리눅스를 돌려보는 것

### 30.2.2 모든 것을 자동화하기! 
상용 인스턴스에 업데이트를 푸시할 때, 모든 단계를 수동으로 하는 것은 좋은 방법이 아니다. 

- **Makefiles**
	- 간단한 프로젝트에 유용하다. 
	- 기능이 제한되어있기 때문에 복잡하게 시스템을 꾸미는 것을 방지한다.
- **Invoke**
	- Fabric 라이브러리의 대를 잇는 도구이다.
	- Fabric과 유사하지만 로컬 환경에서 태스크를 실행하는 기능이 있다. 

### 30.2.3 스테이징 인스턴스 관리하기  
자동화를 하면 프로젝트의 스테이징 서버를 저렴한 서비스 레벨의 자원을 이용해 손쉽게 구성할 수 있다.  
스테이징 환경은 사용 환경에서의 배포를 테스트 할 수 있는 최적의 장소이다.  

### 30.2.4 백업과 롤백으로 장애 대비하기 
때로는 배포로 인해 모든 것을 날릴 수도 있다. (ㅠㅠ)   
변경 사항을 상용으로 반영하기 전에 다음 기능을 PaaS 시스템에서 이용할 수 있는 지 꼭 알아보자.

- 백업으로부터 데이터베이스와 사용자 업로드 파일 복구하기
- 이전 코드 푸시로 롤백하기 

### 30.2.5 외부 백업 유지하기 
PaaS에서 백업을 생성하는 기능을 제공하기도한다.  
하지만 이와 더불어 외부 서비스로 주기적인 백업을 하자. 

