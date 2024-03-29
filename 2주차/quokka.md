## 의존성 역전 원칙
**의존성이 '추상(abstraction)'에 의존하며 '구체(concretion)'에는 의존하지 않는 시스템**

Java의 String 클래스의 예시를 들며, 운영체제나 플랫폼 같이 안정성이 보장된 환경은 무시해도 된다고 설명한다. (어차피 변경되지 않으므로)

### 안정된 추상화
- 인터페이스는 구현체보다 변동성이 낮다
- **인터페이스를 변경하지 않고도, 구현체에 기능을 추가하는 것**. 이것이 소프트웨어 설계의 기본이다.
- 안정된 소프트웨어 아키텍처란 안정된 추상 인터페이스를 선호하는 것이다.

책에서 아래의 코딩 실천법을 설명한다.
1. 변동성이 큰 구체 클래스를 참조하지 마라 > _추상 인터페이스를 참조해라_
2. 변동성이 큰 구체 클래스로부터 파생하지 마라 > _상속은 신중하게 사용해라_
3. 구체 함수를 오버라이드 하지 마라 > _차라리 추상 함수로 선언해라_
4. 구체적이고 변동성이 크면 쓰지마라

예시 1)
```ts
interface IUser { // interface
	createUser: (name:string)=>void;
}

abstract class User {
	abstract createUser(name:string): void;

	deleteUser(name:string) {
		// 자체적인 메서드를 만들 수 있다. 따라서 상속에 용이하나 신중하게 사용해야한다.
	}
}

class Quokka extends {IUser 또는 User}{  
	// 1,2 번 - 변동성이 큰 추상 클래스라면, 인터페이스를 사용하는게 좋다.
}
```

예시 2)
```ts
class User extends Data{
	public createUser(name: string) {
	};
}

class Yena extends User{
	override createUser(name: string): void {
		// 3번 - Data의 의존성을 제거할 수 없게 된다.
	}
}
```

### 팩토리
![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/201d4ed6-7b3d-478a-8495-774c7425821d)

- 소스 코드 의존성은 모두 한 방향, 추상적인 쪽으로 향한다.
- 곡선 = 제어흐름, 코드 의존성 = 직선 -> **제어흐름과 의존성은 반대 방향으로 역전**된다
- 구체적인 것(ConcreteImpl, Service Factory Impl - 업무 규칙을 위한 필요한 모든 세부사항)과 추상적인것들을 분리

> **추상팩토리**
> : 연관성이 있는 객체 군을 묶어 추상화하고, 상황이 주어지면 팩토리 객체에서 구현화하는 생성 패턴 
> ![image](https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/98b7d3d2-8e74-4365-aa6c-fa1cbfa25ec3)
> [참조](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%ACAbstract-Factory-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90#%EC%B6%94%EC%83%81_%ED%8C%A9%ED%86%A0%EB%A6%AC_%ED%8C%A8%ED%84%B4_%EA%B5%AC%EC%A1%B0)

좀 더 자세히 알아보자. [출처](https://dev-momo.tistory.com/entry/%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%ED%8C%A8%ED%84%B4-Abstract-Factory-Pattern)
```js
function takeOutCoffee(type) {
    let coffee;

    if(type === 'latte') coffee = new Latte();
    else if(type === 'espresso') coffee = new Espresso();
    else if(type === 'cappuccino') coffee = new Cappuccino();
    else if(type === 'mocha') coffee = new Mocha(); // 새로운 커피 추가
    // 커피가 추가될 때마다 함수가 변한다.

    coffee.prepare();
    coffee.make();
    coffee.boxing();

    return coffee;
}
```

이 코드에 특정 인스턴스를 만들어주는 클래스(~함수) **팩토리**를 적용하면 아래처럼 된다!
```js
// 커피를 아무리 추가해도 이 함수는 변경되지 않는다!
function takeOutCoffee(type) {
    let coffee = coffeeFactory(type);
	
    coffee.prepare();
    coffee.make();
    coffee.boxing();

    return coffee;
}

// 의존성을 분리해, 가독성을 높였다!
function coffeeFactory(type) {
    let coffee;
    
    if(type === 'latte') coffee = new Latte();
    else if(type === 'espresso') coffee = new Espresso();
    else if(type === 'cappuccino') coffee = new Cappuccino();
    else if(type === 'mocha') coffee = new Mocha();
    
    return coffee;
}
```

하지만 여전히 if문을 계속 정의해야한다는 불편함이 존재한다.
여기에, **추상화 = 만든다는 행위**를 적용하면 아래처럼 된다.
```js
class CoffeeFactory {
    static createCoffee(factory) {
        return factory.createCoffee(); // 인스턴스를 만드는 행위를 추상화
    }
}

// 인스턴스 생성을 서브클래스에 위임해 의존성을 낮춘다
class LatteFactory {
    static createCoffee () {
        return new Latte();
    }
}

class EspressoFactory {
    static createCoffee () {
        return new Espresso();
    }
}

// ...

CoffeeFactory.createCoffee(LatteFactory);
CoffeeFactory.createCoffee(EspressoFactory);
```

자세한 내용은 [이 블로그](https://velog.io/@from_numpy/Typescript-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%B6%94%EC%83%81%ED%99%94-%ED%8C%A9%ED%86%A0%EB%A6%AC-vs-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C#%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%ED%8C%A8%ED%84%B4%EC%9D%80)에 설명이 잘 되어있는 것 같다.

### 구체 컴포넌트
- Main 컴포넌트는 구체 컴포넌트를 최소한 하나 포함한다.
- DIP 위배를 모두 없앨 수는 없다.


### Reference
- [DIP](https://wikidocs.net/167372)
- https://bitkunst.tistory.com/entry/TypeScript-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4
- https://www.zerocho.com/category/JavaScript/post/57b9692ae492d01700b0b75a
- https://dev-momo.tistory.com/entry/%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%ED%8C%A8%ED%84%B4-Abstract-Factory-Pattern

### 기타
- 지난 목요일, 판교 퇴근길 밋업에서 유인동님의 객체지향 TS 강의를 들었는데 아래같은 코드가 있었다.
```ts
abstract class ListView<T extends object, IV extends View<T>> extends View<T[]> { // 상속에 용이하게 abstract 사용한 방식
  itemViews: IV[] = this.data.map((item: T) => this.createItemView(item));

  abstract createItemView(item: T): IV;

  // view를 보여주는 부분
  override template() { 
    return html`<div>${this.itemViews}</div>`
  }
}
```
위 코드를 만들면서, 반복되는 것 같고 + 작은 인터페이스/추상클래스로 분리가 가능한 것들만 빼내는 것이 좋으며 그 기준을 세우는 것이 중요하다고 한 말이 기억에 남았다.
