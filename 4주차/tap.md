# 19. 정책과 수준

> 소프트웨어 시스템이란 정책을 기술한 것(핵심)

좋은 아키텍처라면 각 컴포넌트를 연결할때 의존성의 바향이 컴포넌트의 수준을 기반으로 연결되도록 만들어야한다.

`즉, 저수준 컴포넌트가 고수준 컴포넌트에 의존하도록 설계되어야한다`

![encrypt-diagram](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/93532696/81c2ebd6-7e8c-46aa-8b52-bbed7d1cf7b8)


데이터 흐름과 소스 코드 의존성이 항상 같은 방향을 가리키지는 않는다.

소스코드 의존성을 그 **_수준_** 에 따라 **_결합_** 되어야하며, 데이터 **_흐름_** 을 기준으로 결합되어서는 안 된다.

```js
// 잘못된 예
funciton encrypt() {
    while(true)
        writeChar(translate(readChar()));
}
```
위 예는 잘 못 되었는데, 고수준 `encrypt`가 저수준의 `writeChar`와 `readChar`에 의존되고 있다.

![incryptdiagram2](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/93532696/ae9fb754-0173-4707-b85c-003a4729b80a)

개선한 아키텍처를 확인해보면, `Encrypt 클래스`, `CharWriter`와 `CharReader` 인터페이스를 둘러싸는 점선으로 된 경계다.

경계를 횡단하는 의존성을 모두 경계 안쪽으로 향한다. 이 경계를 기준으로 고수준 저수준 컴포넌트를 구분 짓게 된다.

```ts
class Encrypt {
    string encryptData;
    
    constructor(encryptData: string) {
        this.encryptData = encryptData;
    }
    
    writer() {
        return atob(this.encryptData) // base64 복호화
    }

    reader(data: string) {
        this.encryptData = btoa(data); // base64 암호화
    }
}

// base64 암호화
function ConsoleReader(encrypt: Encrypt, data: string) {
    encrypt.reader('암호화할 데이터');
    const 암호화_데이터 = encryptData.encryptData;
    console.log(암호화_데이터)
}

// base64 복호화
function ConsoleWriter(encrypt: Encrypt) {
    const 복호화_데이터 = encrypt.writer();
    console.log(복호화_데이터);
}

function translateEncrypt() {
    const encrypt = new Encrypt();
    ConsoleReader(encrypt);
    ConsoleWriter(encrypt);
}

```

```ts
// Document 클래스 
class Document {
    prototype: Document;
    new(): Document;
    
    createElement(tagName: string, options?: ElementCreationOptions): HTMLElement;
    setAttribute(qualifiedName: string, value: string): void;
    getAttribute(qualifiedName: string): string | null;
}

const divNode = document.createElement('div');
divNode.setAttribute('id', 'div-id')

const id = divNode.getAttribute('id')
console.log(id)
```

이로써 `reader/writer`는 빈번하게 변경되는 저수준 컴포넌트로 분류 되어 고수준 컴포넌트인 `encrypt`에 영향이 `거의` 없게 된다.

만약 고수준 컴포넌트 변경되어야한다면 더 중요한 이유로 변경될 가능성이 높다.

이는 `Console Reader`와 `Console Writer`가 `Èncryption` 컴포넌트에 `플러그인`된다라고 보면 된다.

이번 장에선 2장에서 나온 `아이젠하워 매트릭스`를 기준으로 중요도를 구분 지어 컴포넌트를 분리하고 묶는 경계를 잡아 보는게 좋을 것 같다.

<br />

# 20. 업무 규칙

1. 핵심 업무 규칙(Critical Business Rule)
2. 핵심 업무 데이터(Critical Business Data)
3. 엔티티(Entity)

사업 자체에 대해 핵심적으로 돈을 벌어다 주는 규칙 + 자동화하지 않더라도 그대로 존재하는 규칙이 `핵심 업무 규칙`, 이 업무에 대한 일정, 상황 보고 등에 대한 데이터들을 `핵심 업무 데이터`, 이런 규칙과 데이터들의 결합하여 하나의 객체로 보며 이 것을 `엔티티`라고 부름.

## 엔티티
![entity](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/93532696/69df050f-f77f-4f97-b806-1ef86a3f1ae3)


엔티티는 시스템 내부의 객체, 핵심 업무 데이터를 기반으로 동작하는 일련의 조그만 `핵심 업무 규칙을 구체화`한 객체

엔티티는 DB, UI, 서드파티 프레임워크에 고려사항들로 오염되면 절대 안된다. 반드시 독립적으로 존재되어야한다.

즉, 엔티티는 순전히 업무에 대한 것이며, 이외 나머지는 고려대상이 아니다.


## 유스 케이스
![use-case](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/93532696/1462c40f-5ad6-4250-81fe-bbf0b52427f3)


유스 케이스는 자동화된 시스템이 사용되는 방법을 설명한다. 또한 사용자가 제공해야 하는 입/출력, 해당 출력을 생성하기 위한 `처리단계를 기술`한다.

엔티티의 핵심 업무 규칙과 반대로 유스 케이스는 애플리케이션 특화된(application-specific) 업무 규칙을 설명한다.

즉, _유스 케이스_ 는 엔티티와 같은 고수준 컴포넌트를 포함하는 `저수준 컴포넌트`의 영역이다.

### Why 엔티티 is 고수준, Why 유스 케이스 is 저수준. I'm 신뢰...

> 왜냐하면 유스케이스는 단일 애플리케이션에 특화되어 있어, 해당 시스템의 입/출력에 보다 가깝게 위치해있기 때문이다.

### 주의할점
엔티티 객체를 가리키는 참조를 요청 및 응답 데이터 구조에 포함하려는 유혹을 받을 수 있다.

하지만 여러 요구 사항들로 인해 _엔티티와 요청/응답 데이터 구조를 결합_ 해 버리면 **공통 폐쇠 원칙(CCP)** 과 **단일 책임 원칙(SRP)** 에 위배가 되고, 쓰레기 데이터와 코드에 수많은 조건문이 추가되버린다.

cf) DB에서 프로시저라는 개념이 있는데 이와 비슷한 상황을 유발할 수 있는 개념이라고 생각한다([프로시저(Procedure)란? (feat. C.R.U.D)](https://fomaios.tistory.com/entry/PLSQL-%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80Procedure%EB%9E%80-feat-CRUD))

### 결론
업무 규칙을 표현하는 코드는 시스템의 **심장부(해당 객체를 인스턴스화 할 수 있는 영역)** 에 위치하고 덜 중요한 코드는 심장부에 **플러그인(인스턴스를 호출하는 영역)** 이 되어야 시스템에서 가장 독립적이고 가장 재사용이 많이 되는 코드가 되어야한다.

<br />

# 21. 소리치는 아키텍처

## 아키텍처의 테마
프레임워크는 `사용하는 도구`일 뿐, 아키텍처가 `준수해야 할 대상이 아니다`. 아키텍처를 프레임워크 중심으로 만들어버리면 유스케이스가 중심이 되는 아키텍처는 절대 나올 수 없다.

## 아키텍처의 목적

좋은 아키텍처는 프레임워크, DB, 웹 서버, 다른 개발 환경 문제나 도구를 선택하는 결정을 미룰수 있게 만드는 환경이다.

좋은 아키텍처는 유스케이스에 중점을 두며, 부수적인 관심사에 대한 결합은 분리시킨다.

`즉, 좋은 아키텍처는 도구 에 구애받지 않은 구조이고, 유스케이스를 중심에 두는 구조이다.`

## 테스트하기 쉬운 아키텍처

프레임워크와 거리를 둔 아키텍처라면 유스케이스에 대한 단위 테스트를 구현하는데 문제가 없을 것이다.

테스트를 돌리는 웹서버가 필요하면 안되고, DB가 연결되어있어도 안되고, 엔티티 객체는 오래된 방식의 간단한 객체여야하며, 여타 복잡한 것들에 `의존되어서는 안된다`.

`즉, 웹 서버를 대체하여 msw를 적용하거나, DB를 대체하여 mock 데이터를 구성하는 등의 실제 돌아가는 환경에 대한 호출하거나 다른 방식으로 가져오는 방식을 테스트 환경에서는 영향이 없어야한다.`(e2e 테스트는 조금 예외라고 생각함)

## 결론

좋은 아키텍처는 `시스템`을 이야기해아하지 프레임워크를 이야기하면 안된다.

새로 합류한 개발자가 시스템이 어떻게 구성되는지 몰라도 시스템의 `유스 케이스를 이해`할 수 있어야한다. 
