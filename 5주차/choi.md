# 5부. 아키텍처

## 24장. 부분적 경계

### 아키텍처

- 아키텍처를 만들고, 유지하는 것에 엄청난 노력이 필요
    - 쌍방향의 다형적 인터페이스
    - I/O 데이터 구조
    - 독립적으로 컴파일/배포 가능한 컴포넌트 격리 & 의존성 관리

### YAGNI (You Aren’t Going to Neet It)

- “너는 그게 필요하지 않아” ⇒ “필요한 작업만 해라”
- 익스트림 프로그래밍의 원칙 중 하나
- 아키텍처 설계에서 YAGNI 원칙을 위배하는 것을 부분적 경계로 해결할 수 있다.

### 부분적 경계 전략 1 | 마지막 단계를 건너뛰기

- 생략 요소
    - 다수의 컴포넌트를 관리하는 작업
- 독립적으로 컴파일/배포 가능한 컴포넌트 격리 작업은 모두 수행한 뒤, 단일 컴포넌트에 그대로 모아만 두는 것
    - 단일 컴포넌트 : 쌍방향 인터페이스, 입출력 데이터 구조 등 모두가 포함된 영역
    - 이 모든 것을 단일 컴포넌트로 컴파일해서 배포
- 버전 번호, 배포 관리 부담이 적음
- “다운로드 후 바로 실행”
    - 사용자가 배포 파일(ex. jar) 간 버전 호환성을 해결하는 작업을 피할 수 있다.

### 부분적 경계 전략 2 | 일차원 경계 | 전략 패턴

- 생략 요소
    - 양방향으로 격리된 상태를 유지하는 쌍방향 Boundary 인터페이스 유지
- 전략 패턴
    - 추후 완벽한 형태의 경계로 확장할 수 있는 구조
      
      ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/c38b797b-81f4-4e32-94de-09100d5918c2)

    - ServiceBoundary 인터페이스는 클라이언트가 사용하며 ServiceImpl 클래스가 구현
    - Client를 ServiceImpl로부터 격리시키는 데 필요한 의존성 역전이 이미 적용된 상태

### 부분적 경계 전략 3 | 퍼사드 패턴

- 위보다 훨씬 단순한 경계
- 의존성 역전을 포기
- Facade 클래스에는 모든 서비스 클래스를 메서드 형태로 정의
- 서비스 호출이 발생하면 해당 서비스 클래스로 호출을 전달
- 클라이언트는 서비스 클래스에 직접 접근 불가능
  
  ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/15456a32-d459-4be5-a314-2368d2ff4b40)

- Client가 모든 서비스 클래스에 대해 추이 종속성을 가짐
    - 정적 언어라면, 서비스 클래스 중 소스 코드가 변경되면 Client는 무조건 재 컴파일

### 요약

- 아키텍처의 경계가 언제 어디에 존재해야 할지, 완벽하게 혹은  부분적으로 구현할지 결정하는 일 또한 아키텍트의 역할

## 25장. 계층과 경계

- 시스템이 세 가지 컴포넌트(UI, Business Rules, DB)로만 구성된다고 생각하기 쉽지만 대다수의 시스템에서 컴포넌트 갯수는 훨씬 많다.
- 해당 장은 움퍼스 사냥 게임을 예시로 구조를 설명

### 움퍼스 사냥 게임
![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/a0133465-5d12-4d3a-b892-479c26980511)

- 1972년 발매한 모험 게임
- 텍스트 기반 단순한 명령어로 동작 (GO EAST, SHOOT WEST…)
    1. 게임 규칙과 텍스트 기반 UI를 분리해서 다국어로 발매한다면?
       
       ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/3bae7505-e7b7-4861-b737-dfa757a05adc)

       
    2. 게임의 상태를 영속적 저장소에 유지한다면? (플래시 메모리, 클라우드, RAM 등)
       
        ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/92a97516-d60f-4c97-ba8f-7fb8057ef047)

        - 게임 규칙이 다양항한 언어, 데이터 저장소에 대해 알 필요가 없는 구조
        - 의존성 규칙을 준수할 수 있도록 의존성이 적절한 방향을 가리키게 만들어야 함
    

### 클린 아키텍처?
- 사용자의 명령어 UI 매커니즘이 다양화 된다면? (Shell, text, 채팅 앱 등)
    - 점선 테두리는 API를 정의하는 추상 컴포넌트
    - API는 구현하는 쪽이 아닌 사용하는 쪽에 정의되고 소속된다.
    - API는 의존성 흐름의 상위에 위치한 컴포넌트에 속한다.
    
    ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/3f3f661a-6778-440d-be14-72747da8795d)

- 다이어그램 단순화하기
    - 순전히 API 컴포넌트만 집중
    - 모든 화살표가 위를 향하도록 맞춰진 형태
        - 화살표는 데이터 흐름의 방향이 아니라 소스 코드의 의존성의 방향
    - 데이터 흐름을 두 개의 흐름으로 분리
        - 왼쪽의 흐름은 사용자와의 통신 관여
        - 오른쪽의 흐름은 데이터 영속성에 관여
    - Game Rules는 데이터에 대한 최종적인 처리기
        - 상단에 위치한 컴포넌트를 중앙 변환(Central Transform) 이라고 칭함

![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/9159e45f-11b0-412a-b9b4-e1c05e3acf17)

### 흐름 횡단하기

- 움퍼스 사냥 게임을 네트워크상에서 여러사람이 함께 플레이할 수 있게 만든다면?
    - 시스템이 복잡해질수록 컴포넌트 구조는 더 많은 흐름으로 분리될 것
    
    ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/1e47c2b9-cd38-479a-bfe7-b1962a98f7c6)

### 흐름 분리하기

- 모든 흐름이 상단의 단일 컴포넌트에서 만난다면 좋겠지만, 현실은 훨씬 복잡
    - 게임 규칙 중 일부인 지도 관련 메커니즘을 처리하는 MoveManagement 추가
    - 해당 구조는 저수준 정책이 고수준 정책에게 알려 PlayManagement에서 상태를 관리
- 이보다 더 높은 수준의 정책 집합이 존재할 수 있다.

![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/d08e8eeb-96a4-43b0-bc56-faf11eed64c4)

### 마이크로 서비스 추가해보기

- 대규모의 플레이어가 동시에 플레이한다면?
    - MoveManagement는 플레이어의 컴퓨터에서 직접 처리
    - PlayManagement는 서버에서 처리
    - PM은 접속된 모든 MM 컴포넌트에 마이크로서비스 API를 제공
    - PM과 MM 사이에 아키텍처 경계가 생성

![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/25587196/c54e5667-a11a-4c21-82a9-2920033a1c26)

### 요약
- 프로그램의 크기와 상관 없이 아키텍처 경계는 어디에나 존재한다.
    - 프로젝트 초기에 이에 대한 파악은 쉽지 않다.
- 경계가 무시된 상태에서 경계를 추가하는 비용은 굉장히 크다.
- 오버엔지니어링은 언더엔지니어링보다 나쁠 때가 훨씬 많다. (YAGNI의 철학)
- 아키텍트의 역할은 시스템이 발전함에 따라 주의하고 경계가 존재하지 않아 생기는 문제점을 초기에 찾아내어 구현 비용과 감수 비용을 구분하는 것이다.
