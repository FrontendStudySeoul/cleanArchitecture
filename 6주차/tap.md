# 클린 임베디드 아키텍처

> '소프트웨어는 닳지 않지만, 펌웨어와 하드웨어는 낡아 가므로 결국 소프트웨어도 수정해야 한다.'
>
> '더그 슈미트'
> 
- 펌웨어는 ROM, EPROM 혹은 플래시 메모리 같은 비휘발성 메모리에 유지된다.
- 펌웨어는 하드웨어 장치에 프로그래밍된 소프트웨어 프로그램 혹은 명령어 집합이다.
- 펌웨어는 개별 하드웨어에 내장되는 소프트웨어다.
- 펌웨어는 읽기 전용 메모리(ROM)에 쓰여진 소프트웨어(프로그램이거나 데이터)다.

> 펌웨어를 수없이 양산하는 일을 멈추고, 코드에게 유효 수명을 길게 늘리 수 있는 기회를 주어라.(iOS, Android가 대표적이지 않을까)

## 앱-티튜드 테스트

**캔트 백의 소프트웨어 구축의 세가 지 활동**
- "먼저 동작하게 만들어라": 소프트웨어가 동작하지 않는다면 사업은 망한다.
- "올바르게 만들어라": 코드를 리팩터링해서 사람들을 이해하게 만들고, 요구사항이 변경될 때 개선할 수 있도록 만들어라.
- "빠르게 만들어라": 코드를 리팩터링해서 '요구되는' 성능으 만족시켜라.

**프레드 브룩스(맨먼스 미신)** 에서도 "버리기 위한 계획을 세우라"라고 제안한다. 
이 둘은 동일 충고를 하고 있으며, **동작하는 것을 배워라, 그리고 나서 더 나은 해결책을 만들어라** 라고한다.

위 내용은 모든 소프트웨어 산업에 통용되는 말이다. 코드의 수명을 늘리는데 관심이 없고, "동작하도록만 만들어진다."

즉, 앱이 동작하게만 만드는 것을 개발자용 "앱-티튜드 테스트"라고 부른다. 오직 앱이 동작하는 데만 신경쓰는 행위는 자신의 제품과 고용주에게 몹쓸 짓을 하는 것.
이는 앱-티튜드 테스트는 통과할지언정 "클린 임베디드 아키텍처"를 가진다고는 말할수 없다.

## 타깃-하드웨어 병목현상

![펌웨어 + 하드웨어](https://private-user-images.githubusercontent.com/93532696/326227226-560c1a66-58ac-4703-b43a-7487e203e93d.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQyODA5MzgsIm5iZiI6MTcxNDI4MDYzOCwicGF0aCI6Ii85MzUzMjY5Ni8zMjYyMjcyMjYtNTYwYzFhNjYtNThhYy00NzAzLWI0M2EtNzQ4N2UyMDNlOTNkLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA0MjglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwNDI4VDA1MDM1OFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWZmNDU2ZjUzYzg4NTVhZGJiMDc3NjZjZjU3YWNmNGExMDQwMzIwYjMyNTVmYWMxYTk0MjllMDZkYzkzYzgzNTUmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.s5zA0s-acJF837a-gaESg5Gm1XZ2-efAbpSULVqwdbk)

소프트웨어와 펌웨어가 서로 섞이는 안티패턴이 발생한다면 코드 변경도 어렵고 변경자체가 위험을 수반하게 된다.
이는 가벼운 수정에도 전체 시스템 회귀 테스트가 필요하게된다.

### 하드웨어는 "세부사항"이다.

![HAL-하드웨어 추상화 계층](https://private-user-images.githubusercontent.com/93532696/326227244-bce3ed0f-2ae2-4130-8497-57af1e5687cc.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQyODA5MzgsIm5iZiI6MTcxNDI4MDYzOCwicGF0aCI6Ii85MzUzMjY5Ni8zMjYyMjcyNDQtYmNlM2VkMGYtMmFlMi00MTMwLTg0OTctNTdhZjFlNTY4N2NjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA0MjglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwNDI4VDA1MDM1OFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTVkYjkwOTY1NWIyYWUwNjllYzJiYzBhZTc4NDFhNDc5MGE0YmRmNjg4MWY4ODlkNjQ5YmUzMDhmNDdhNWEyNzImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.W2zShtSHqugmrW6ldqEimeEL21OhvhPlT5xHozyZu1U)

소프트웨어와 펌웨어 사이의 경계는 **하드웨어 추상화 계층(Hardware Abstraction Layer, HAL)** 이라고 부른다.
- HAL은 자신보다 위에 있는 소프트웨어를 위해 존재, API는 소프트웨어의 필요성에 맞게 만들어져야한다.
- 펌웨어가 바이트나 배열을 플래시 메모리에 저장하던지, 소프트웨어는 이름/값 쌍으로 플레시 메모리, 하드 디스크, 클라우드에 저장되든지 펌웨어나 소프트웨어가 이를 몰라야한다.

_즉, 중간에서 이루어지는 것들은 HAL이 제공하고, 어떻게 동작되는지는 소프트웨어가 알지 못해야한다._

### 프로세스/운영체제는 "세부사항"이다.

보통 임베디드 시스템에선 HAL만으로 충분하지만, 실시간 운영체제(RTOS)나 리눅스/윈도우를 사용한다면? 이때는 세부사항을 운영체제로 취급해야하고, 운영체제에 의존하는 일을 막아야한다.
이때 클린 임베디드 아키텍처에서는 운영체제 추상화계층(OSAL)을 통해 격리시킨다.
 
![운영체제 추상화 계층](https://private-user-images.githubusercontent.com/93532696/326227249-8e6f2f96-519d-4302-98cd-58a726808e0b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQyODA5MzgsIm5iZiI6MTcxNDI4MDYzOCwicGF0aCI6Ii85MzUzMjY5Ni8zMjYyMjcyNDktOGU2ZjJmOTYtNTE5ZC00MzAyLTk4Y2QtNThhNzI2ODA4ZTBiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA0MjglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwNDI4VDA1MDM1OFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWQwZGJiYTU1Yjg1YTJkMGU5OWJmY2JmMTY5N2NiN2M4Njk4NDY1ZGE5MDc3MDIwNjNhZjNkOTY1NWNkNTkwZmEmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.JMPfh7GWCJwEA5mMOF6emZKRf0RAFAAet3KXL3CiGqA)

이 처럼 소프트웨어가 운영체제에 직접 의존하지 않고, OSAL에 의존한다면 대부분의 작업을 수정하는 일과 정의된 인터페이스에 맞게 코드를 작성이 가능해지게 된다.
하지만 이렇게 추상 단계를 만들게 되면, 결국 코드가 '비대화'가 발생하게되는데,(결국 OS에 관련된 중복된 정보가 OSAL에서도 작성되어야하기 때문) 이는 애플리케이션에 공통 구조를 만드는데 리소스를 드는 일이기 때문에 상대적으로 큰 문제가 되지 않는다.

_즉, 추상 계층을 만들게 되면 실제 '세부사항'의 영역에 크게 의존하지 않게 관리가 가능해지며, 환경에 상관없이 '테스트'가 가능해지기 때문이다._

## 인터페이스를 통하고 대체 가능성을 높이는 방향으로 프로그래밍하라

모든 주요 계층(S/W, H/W, OS 등) 내부에는 이 원칙을 적용시켜 관심사를 분리시키고 인터페이스를 활용하여 대체 가능성을 높이는 방향으로 프로그래밍 유도가 가능하다.
이는 계층형 아키텍처(Layer Architecture)는 인터페이스를 통해 프로그래밍하는 발상으로 기반된다.

경험법칙에 따르면 인터페이스 정의는 헤더 파일에 해야한다. 헤더 파일에는 함수 선언과 함수에서 사용되는 상수와 구조체 이름만 포함시켜야한다.

> 구현체제에서 필요한 데이터 구조, 상수, 타입 정의들로 인터페이스 헤더 파일을 어지럽히지 말것(그렇게 되면 결국 원치 않은 의존성을 만들어 낸다)

1. 구현 세부사항의 가시성을 제한
2. 구현 세부사항은 변경될 것이라고 가정
3. 세부사항을 알고있는 부분이 적을수록 추적하고 변경해야할 코드가 적어짐

결국 클린 임베디드 아키텍처는 '모듈'들이 '인터페이스'를 통해 상호작용한다. 때문에 각 계층 내부에 테스트가 가능한 구조다. 인터페이스는 타깃과 별개로 테스트할 수 있도록 경계층 또는 대체 지점을 제공한다.

## DRY(Don't Repeat Youerself) 원칙: 조건부 컴파일 지시자를 반복하지 말것

> 'Don't Repeat yourself "( DRY )는 변경될 가능성이 있는 정보 의 반복을 줄이고 변경 가능성이 적은 추상화 로 대체하거나 애초에 중복을 방지하는 데이터 정규화를 사용하는 것을 목표로 하는 소프트웨어 개발 원칙.'
>
> '[위키디피아](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)'
> 

 즉, 코드를 반복하는 일을 반복하지 말것. 평소 조건부 컴파일이나 런타임 환경에서 특정 코드를 활/비활성화시키는 로직을 많이 작성한다. 그런데 이런 구문이 반복되어서 나타내지 말라는 것.

하드웨어 추상화 계층이 있다면? HAL 뒤에 가려진 세부사항이 될 것. 만약 HAL이 조건부 컴파일 대신 사용할 수 있는 일련의 인터페이스를 제공한다며?
우리는 링커 또는 다른 형태의 실시간 바인딩을 사용해서 소프트웨어를 하드웨어와 연결할 수 있다.

![클린 아키텍처](https://private-user-images.githubusercontent.com/93532696/326230459-33c6e22b-7e67-4835-884b-61dc6e974156.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQyODA0NzUsIm5iZiI6MTcxNDI4MDE3NSwicGF0aCI6Ii85MzUzMjY5Ni8zMjYyMzA0NTktMzNjNmUyMmItN2U2Ny00ODM1LTg4NGItNjFkYzZlOTc0MTU2LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA0MjglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwNDI4VDA0NTYxNVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWMzOWE1MjU5MjAzMmUxZWMyNjk0YmJjMWJjYjI0ZGMzM2RhZTJjODJjYjU3YmYxZThkMWZmZGMzYWViYmMxN2YmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.6oHiWzgoVsj24brK0rRRMwIFsv-KMokhDtPo2bR3RU4)

결국, 추상화와 세부사항의 분리는 클린 아키텍처에서 중요한 원칙이며, 추상화된 인터페이스를 통해 내부 로직과 외부 요소 간의 결합도를 줄이며, 이는 시스템의 모듈성과 유연성을 증진시키는 것이 목적.
