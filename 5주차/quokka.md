
## 들어가기전에.. 🧐
### 서비스지향아키텍처(SOA)란? 
각 기능들이 각각의 서비스를 독립적으로 제공할 수 있도록 설계된 SW 아키텍처

<img width="840" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/aa0af556-50c8-4594-8f83-add221cf2f6b">

> 차량에 제공가능한 여러 기능들을 서비스로 정리하는 예시 [참고](https://www.youtube.com/watch?v=gXtzPh9k2Ek)

어플리케이션은 Service Bus를 통해 다른 서비스들과의 네트워크(탐색)를 할 수 있다. 
어플리케이션 별로 특정 서비스(객체)를 제공/이용할 수 있으며, 신규 서비스를 자유롭게 등록할 수도 있다.
아래 사진에서 App2는 App1의 서비스와 App3의 서비스를 사용한 신규 서비스를 제공해주는 것을 볼 수 있다.

<img width="786" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/38e53c0f-ac2f-45ef-a878-fd4b4df573e1">


### 마이크로서비스 아키텍처란? 
독립적으로 수행되는 소규모의 시스템들이 명확하고 절대적으로 분리된 인터페이스 정의에 의해 긴밀하게 통신하는 아키텍처 기반의 접근, 구현 방식이다. 쉽게말해, **서비스별로 각기 다른 서버와 DB를 구성하는 것**을 의미한다. [출처](https://freeend.tistory.com/115)

<img width="504" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/54fc0090-ec92-4523-ac5c-65045aa2817b">


더 자세한 내용은 [이 블로그](https://medium.com/@SoftwareDevelopmentCommunity/what-is-service-oriented-architecture-fa894d11a7ec)에 잘 나와있다!
- [서비스 아키텍처 vs 마이크로서비스](https://www.quora.com/What-is-the-difference-between-SOA-and-microservices)

<br/>
<br/>

## 서비스 아키텍처?
**서비스 자체로는 아키텍처를 정의하지 않는다.** 
시스템 아키텍처의 의미는 "의존성 규칙을 준수하며 고수준의 정책을 저수준의 세부사항으로부터 분리하는 경계"에 의해 정의되지만, 서비스는 단순히 "애플리케이션의 행위를 분리"하는 것이라고 책에서는 설명하고 있다.
<br/>

## 서비스의 이점?
서비스를 분리함으로써 몇가지 이점을 가질 수 있지만, 이에 대한 이의를 책에서 제시하고 있다.
<br/>

### 1. 결합분리의 오류
서비스 사이의 결합이 확실히 분리된다는 장점이 있다. 서비스는 다른 서비스의 변수에 직접 접근할 수 없다.
그리고 모든 서비스의 인터페이스는 반드시 잘 정의되어 있어야한다.

→ 프로세서 내의 또는 네트워크 상의 공유 자원 때문에 결합될 가능성이 여전히 존재한다. 더욱이 서로 공유하는 
데이터에 의해 이들 서비스는 강력하게 결합되어 버린다.
→ 서비스 인터페이스나 함수 인터페이스나 별반 차이는 없다.

> 실제 위 예시에서 확인할 수 있었던 문제이다!
<br/>

### 2. 개발 및 배포 독립성의 오류
전담팀이 서비스를 소유하고 운영해 **개발 및 배포독립성**을 가진다는 특징을 가지며, 따라서 **확장 가능**한 것으로 간주된다.
대규모 엔터프라이즈 시스템을 독립적으로 개발 & 배포할 수 있다.

→ 대규모 엔터프라이즈 시스템은 다른 시스템으로도 구축할 수 있다. 
→ 항상 독립적으로 배포, 운영, 개발할 수 없다. 어느정도 결합된 정도에 맞게 조정해야한다.
<br/>

### 3. 야옹이 문제

<img width="1066" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/f80eef18-375e-47e7-97b4-a850a1d49561">


위처럼 설계한 시스템에서 야옹이 배달 서비스가 신규로 들어오게 된다면, 이 서비스중 어디를 변경해야 할까?
**전부다.**
서비스들이 모두 결합되어 있어 독립적으로 개발, 배포, 유지할 수 없다.

이게 바로 횡단 관심사가 가지는 문제다.
<br/>

## 객체가 구출하다. - SOLID 설계 원칙이 해결한 방법

<img width="635" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/74160387-1737-44b8-88e0-635719238c97">


배차 관련 로직은 Rides에, 야옹이 관련 로직은 Kittens 컴포넌트로 추출한다.
팩토리가 이 클래스들을 생성하고 있으며, 두 컴포넌트는 기존 컴포넌트들에 있는 추상 기반 클래스를 템플릿 메서드나 전략 패턴을 통해 오버라이드 한다.
모든 의존성들이 의존성 규칙을 준수하고 있다는 점을 주목하자.

이 전략을 적용해 다시 독립적으로 개발 & 배포할 수 있게 되었다!
<br/>

## 컴포넌트 기반 서비스 
서비스도 위 SOLID 원칙대로 설계할 수 있고, 컴포넌트 구조를 갖출 수 있다.

<img width="662" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/cf26d031-549b-4ef2-a04d-c5edf27c9743">


서비스 = 추상 클래스의 집함
새로운 클래스(Ride, Kitty)는 이 추상클래스를 확장해서 만들어진다. 
각 **서비스의 내부는 자신만의 컴포넌트 설계**로 되어있어 파생 클래스를 만드는 방식으로 신규 기능을 추가할 수 있다.
<br/>

## 횡단 관심사
아키텍처는 서비스를 관통하며, 서비스를 컴포넌트 단위로 분할한다는 점을 기억하자.
횡단 관심사를 처리하려면, **서비스 내부는 의존성 규칙도 준수하는 컴포넌트아키텍처로 설계해야한다.**
<br/>

## 결론
서비스 자체로는 아키텍처적으로 중요한 요소가 아니다.
시스템 아키텍처는 시스템 내부에 그어진 경계와 경계를 넘나드는 의존성에 의해 정의된다. 
