# 3부 설계원칙
## 8. OCP(계방폐쇄원칙 - Open Close Principle)
OCP란 시스템을 확장하기 쉬운 동시에 변경으로 인해 시스템이 많은 영향을 받지 않도록 설계하는 중요 원칙중 하나이다.<br>
컴포넌트는 단방향으로 흘러야하며 이를 어길시에는 의존성 역전원칙을 통해 주입해줘야한다.

### 프론트엔드에서의 OCP
우선 리액트로 예시로 들 수 있다. 리액트에서 흔히 props로 어떤 값을 넘겨받을지에 대한 인터페이스를 설정해줄 수 있다.
<br>
가령 아래와 같은 ButtonComponent가 있다.

```tsx
//Button.tsx
import { cn } from "@/utils/classname";
import React from "react";
import { ButtonStyle } from "./style";

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  className?: string;
  color?: "black" | "white" | "red";
  children?: React.ReactElement | string;
}

const Button = ({
  className,
  children,
  color = "white",
  ...props
}: ButtonProps) => {
  return (
    <button {...props} className={`${className} ${cn(ButtonStyle({ color }))}`}>
      {children}
    </button>
  );
};

export default Button;

//style.ts
import { cva } from "class-variance-authority";

const ButtonStyle = cva(["px-4 h-11 rounded-[4px]"], {
  variants: {
    color: {
      black: ["bg-black text-white hover:bg-gray-800"],
      white: ["border border-black hover:bg-gray-100"],
      red: ["bg-red-500 hover:bg-red-600 text-white"],
    },
  },
});

export { ButtonStyle };

```
위 컴포넌트에서 개방폐쇄원칙을 지키면서 Button의 기능을 추가하려면 어떻게 해야 할까?<br>
?연산자로 props로만들어주고 기존 기능에는 영향이 가지 않도록 만들 수 있다.<br>
또한 Typescript를 사용하여 인터페이스 수준으로 OCP를 적용할 수 있다.
```tsx
type CouponListItemType =
  | {
      type: "rate";
      title: string;
      discountRate: number;
    }
  | {
      type: "amount";
      title: string;
      discountAmount: number;
    };
```

마지막으로 커스텀 훅을 통한 방법이 있다.<br>
만약 내 컴포넌트에서 특정 기능을 구현하려고 하면 어떻게 할까?
<br>
나는 주로 커스텀 훅으로 구현한다. 그러면 커스텀훅이라는 구현부를 재사용하기도 수월하고 기존 컴포넌트에서 기능을 추가하여 다른곳에 이펙트를 줄 확률이 줄어든다.
```tsx
import { useCallback, useEffect, useRef } from "react";

type IntersectHandler = (
  entry: IntersectionObserverEntry,
  observer: IntersectionObserver
) => void;

export const useIntersect = (
  onIntersect: IntersectHandler,
  options?: IntersectionObserverInit
) => {
  const ref = useRef<HTMLDivElement>(null);
  const callback = useCallback(
    (entries: IntersectionObserverEntry[], observer: IntersectionObserver) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) onIntersect(entry, observer);
      });
    },
    [onIntersect]
  );

  useEffect(() => {
    if (!ref.current) return;
    const observer = new IntersectionObserver(callback, options);
    observer.observe(ref.current);
    return () => observer.disconnect();
  }, [ref, options, callback]);

  return ref;
};

```

## 9. LSP(리스코프 치환 원칙 - Liskov Substitution Principle)
상속 관계의 특징을 정의하기 위해 발표한 것으로, 서브 타입은 언제나 기반 타입으로 교체할 수 있어야 한다는 것을 뜻한다.
<br>
그리고 아키텍처 관점에서 LSP를 이해하는 최선의 방법은 이 원칙을 어겼을 때 시스템 아키텍처에서 무슨 일이 일어나는지 관찰하는것이다.

### 프론트엔드에서의 LSP
예를들어 우리는 디자인시스템을 구축해야하고 input, button과 같은 기본 컴포넌트들을 만들떄 기본 인터페이스를 상속하여 개발한다.
<br>
```tsx
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
...props
}

const Button = ({
  ...props
}: ButtonProps) => {
  return (
    <button {...props} >
    </button>
  );
};

export default Button;

interface ButtonProps extends React.InputHTMLAttributes<HTMLInputElement> {
...props
}

const Input = ({
  ...props
}: InputProps) => {
  return (
    <Input {...props} >
    </Input>
  );
};

export default Input;

```
그럼 이 책에서 말한것처럼 위를 지키지 않았을때 어떻게해야할까?<br>
그럼 만약에 기존 html input태그에서 가지지 않은 property를 사용하기 위해 매번 디자인시스템에 손 대 property를 추가하거나, 새로운 기능이 나왔을때 해당 property가 없다면 새로운 props를 만들어서 구현해야한다.
<br>
그리고 고차 컴포넌트도 LSP 원칙을 지킨 예시중에 하나인것 같다. 예를들어 버튼에 로깅하는 로직을 추가해야할때 새로운 로깅버튼을 추가하거나 props를 추가하는것도 하나의 방법이지만 LSP를 지켜 고차컴포넌트로 만들 수 있다.
<br>
```tsx
interface ButtonProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
}

function BaseButton({label, onClick}: ButtonProps) {
  return (
    <button onClick={onClick}>{label}</button> 
  );
}

function enhanceWithLogging(Component: React.FC<ButtonProps>) {
  return (props: ButtonProps) => {
    const onClick = () => {
      console.log("clicked!");
      props.onClick();
    };

    return <Component {...props} onClick={onClick} />;
  }
}
```
하지만 상속만 잘한다고 LSP를 지키는것이 아니다. 만약 다음과같이 onClick의 기능 자체가 달라지면 안된다. 이를 잘못된 오버라이딩이라 한다.
<br>
LSP를 엄격하게 지킨 코드일수록 오버라이딩을 지양해야하며, 오버라이딩을 편하게 사용할수록 LSP는 지켜지지 않는것이다.


```tsx
interface ButtonProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  onClick:(clicks:number)=>void
}

function BaseButton({label, onClick}: ButtonProps) {
  return (
    <button onClick={onClick}>{label}</button> 
  );
}

function enhanceWithLogging(Component: React.FC<ButtonProps>) {
  return (props: ButtonProps) => {
    const onClick = () => {
      console.log("clicked!");
      props.onClick();
    };

    return <Component {...props} onClick={onClick} />;
  }
}
```
