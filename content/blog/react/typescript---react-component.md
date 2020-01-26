---
title: TypeScript - React Component
date: 2020-01-15 19:01:61
category: react
---

# Start React With TypeScript



### 1. 프로젝트 생성

```bash
$ create-react-app start-react-with-ts --template typescript
```

기존 리액트 프로젝트를 생성하는 CLI에서 옵션으로 `--template typescript` 주면 된다. ( `--typescript`만 해도 가능 )

생성 후 프로젝트를 확인 해보면 typescript 기반 리액트 컴포넌트는 `.tsx` 를 확장자로 갖는다.

### 2. React.FC

```react
const App: React.FC = () => {
  return (
   {...}
  );
}
```

 `App.tsx` 파일을 보면 App 이라는 함수형 컴포넌트를 `React.FC` 타입을 이용하여 선언 해 주었다.  이렇게 선언하는 것은 장단점이 존재 한다. 장단점을 살펴보기 위해 Hello라는 컴포넌트를 만들어 보자.

##### Hello.tsx

```react
type helloProps = {
  name: string
  say: string
}

const Hello: React.FC<helloProps> = ({name, say}) => {
  return(
  	<div>Hi my name is {name}. {say}!</div>
  )
}
```

`React.FC` 타입은 `Generic` 으로 컴포넌트가 받아야 할 props 들을 타입으로 받는다. 예제 에서는 `type alias` 를 이용하여 props의 타입을 지정 해 줬는데, `interface` 를 사용해도 상관 없다. 단, 일관성 있게만 사용하자.

`React.FC` 타입의 첫번째 장점은

![image](https://user-images.githubusercontent.com/39187116/72000745-4ce0dc00-3287-11ea-89fd-8745c4578100.png)

props의 default 값으로 children이 들어가 있다는 것 이다. ( 근데 사실 크게 장점이라고 말하기 좀 그렇다... )

두번째 장점은 `defaultProps, propTypes, contextTypes` 를 지정할 때 자동완성이 된다는 점이다.

![image](https://user-images.githubusercontent.com/39187116/72000887-9e896680-3287-11ea-89ec-d1e53cb3dc87.png)

하지만 `defaultProps` 를 지정할 때 문제가 있다.**`React.FC`를 이용한 타입 지정은 defaultProps가 동작하지 않는다.**  예시로 name props는 값을 부모 컴포넌트에서 받고 defaultProps로 say 값만 지정한다고 한다고 할 때 타입이 올바르지 않다고 컴파일 에러가 발생한다.

따라서 `React.FC` 는 별로 좋지 않은 것 같다. `React.FC` 를 지워서 같은 기능을 하는 Nice 컴포넌트를 만들어 보자.

```react
type niceProps = {
  name: string
  say: string
}

const Nice = ({name, say}: niceProps) => {
  return(
     <div>Hi my name is {name}. {say}!</div>
  )
}

Nice.defaultProps = {
  name:'donguk',
  say:'thank you'
}
```

`React.FC` 를 지우고 객체(`props`)에 타입을 지정하였다. 이렇게 하면 `defaultProps` 를 지정해도 잘 동작한다. 



### 2. 생략할 수 있는 Props 설정

생략 할 수 있는 `props`를 설정 할 때에는 ? 를 사용하면 된다.

```react
type niceProps = {
  name: string
  say: string
  options?: string
}

const Nice = ({name, say, options}: niceProps) => {
  return(
    <>
    	<div>Hi my name is {name}. {say}!</div>
    	{options && <div>Here is {options}</div>}
    </>
  )
}

Nice.defaultProps = {
  name:'donguk',
  say:'thank you'
}
```



### 3. 함수를 props로 받기

##### Nice.tsx

```react
type niceProps = {
  name: string
  say: string
  options?: string
  func: (name: string) => void // return nothing
}
  
  const Nice = ({name, say, options, func}: niceProps) => {
  return(
    <>
    	<div>Hi my name is {name}. {say}!</div>
    	{options && <div>Here is {options}</div>}
      <button onClick={()=>func(name)}>Click</button>
    </>
  )
}

Nice.defaultProps = {
  name:'donguk',
  say:'thank you'
}
```

#### App.tsx

```react
const App: React.FC = () => {
    const handleClick = (name: string) => {
        console.log('name name', name)
    }
    return (
        <>
            <Hello name="donguk" say="thank you"/>
            <Nice func={handleClick}/>
        </>
    );
}
```

