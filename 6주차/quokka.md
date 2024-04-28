> "테스트는 시스템의 일부이며, 아키텍처에도 관여한다."

## 시스템 컴포넌트인 테스트
<img width="763" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/5639971b-3ab5-4771-876c-b2c5177e712d">

- 테스트는 태생적으로 **의존성 규칙을 따른다**. 의존성이 테스트 대상이 되는 코드를 향한다. 다시말해, 테스트는 시스템 컴포넌트를 향해 항상 원의 안쪽으로 이동한다.
- 테스트는 독립적으로 배포 가능하다.
- 테스트의 역할은 운영이 아니라 개발을 지원하는 데 있다. 따라서 운영에 꼭 필요하지는 않다.

## 테스트를 고려한 설계
테스트 시스템의 설계와 잘 통합되지 않으면, 테스트는 깨지기 쉬워지고 시스템은 변경하기 어려워진다.

> **깨지기 쉬운 테스트** <br/>
> 기능은 잘 동작하는데 테스트가 깨지는 경우를 "거짓양성" 이라 하는데,
> 이런 거짓 양성이 많은 테스트를 Fragile Tests(깨지기 쉬운 테스트)라고 한다.

책에서 "시스템의 공통 컴포넌트가 변경되면 수백, 심지어 수천 개의 테스트가 망가진다" 라고 하고 있다.
예를 들어, 공통 컴포넌트 `<Form />`이 아래처럼 있다고 생각해보자.

```html 
<input />
<span id="invalid_input_error">  
	User Name Required  
</span>
```

그리고 이를 테스트하는 코드가 있다.
```js
test('User Name Invalid', async () => {  
	render(<Form />)  

	await user.click(
		screen.getByRole('button'); // 이름을 입력하지 않고 버튼을 선택
	)
		   
	expect(await screen.findByText('User Name Required')) // 꺠지기 쉬운 테스트!
	...
})
```
span 태그 안의 text가 'Name Required'로 변경된다면 어떻게 될까?
위의 `<Form />`태그가 공통 컴포넌트로 사용되어 있어, 모든 테스트에 위 내용이 포함되어 있다면 **전부 변경**해줘야한다.

이를 "기능은 제대로 동작하는데, 테스트를 깨지는 경우"인 **깨지기 쉬운 테스트**라 한다.   
즉, 테스트는 "핵심 대상"을 테스트 해야하며 세부사항을 테스트해서는 안된다.
책에서 강조한 **결합**이 바로 이 예시라고 생각한다.

"변동성이 있는 것에 의존하지 말라."

> 💡 그래서 react-testing-library에서 `findByRole`, `findById`등의 변하지 않는 값을 제공하는 것!
> 위 코드에서 깨지기 쉬운 테스트를 변경하면 아래처럼 될 것이다.
> 
> `expect(screen.getByTestId('invlid_input_error').not.toBeDisabled())`


## 테스트 API
GUI는 변동성이 커서 테스트가 깨지기 쉽기 때문에, GUI를 사용하지 않고 업무 규칙을 테스트할 수 있게 해야한다. 
이를 위해서는 **테스트가 모든 업무 규칙을 검증하는데 사용할 수 있도록 특화된 API**를 만들면 된다.
테스트 API는 **테스트를 애플리케이션으로부터 분리할 목적**으로 사용한다.

### 구조적 결합
애플리케이션의 구조를 테스트로부터 숨기는 테스트 API를 만들자. 그럼 상용 코드를 리팩터링하거나 변화시켜도 테스트에는 전혀 영향을 주지 않는다.
즉, 테스트 코드와 실제 코드를 따로 분리해 사용하자.

### 보안
위험한 부분의 구현부는 독립적으로 배포할 수 있는 컴포넌트로 분리하자.

> **FE의 테스트**<br/>
> <img width="509" alt="image" src="https://github.com/FrontendStudySeoul/cleanArchitecture/assets/70925962/fc41fc2b-8631-44d1-926a-f3838961ca31"> <br/>
> 에서 E2E 테스트를 제외한 부분에 대한 내용인 것 같다. <br/>
> msw 를 사용한 테스트가 생각나는 느낌..


## 결론
테스트를 시스템 일부로 설계하자. 잘 설계하면 안정성과 회귀의 이점을 얻을 수 있을것이다.


## 읽어보면 좋을 내용
- [javascript-testing-best-practice](https://github.com/goldbergyoni/javascript-testing-best-practices/blob/master/readme.kr.md#%EC%84%B9%EC%85%98-3%EF%B8%8F%E2%83%A3-%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8)
- [좋은 단위 테스트의 4대 요소](https://namhoon.kim/2022/11/08/method-test/030/index.html)
- [MSW를 활용하는 Front-End 통합테스트](https://fe-developers.kakaoent.com/2022/220825-msw-integration-testing/)
