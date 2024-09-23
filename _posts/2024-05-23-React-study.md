---
# layout : post - 기본적으로 설정되어 있는 변수
title: React Study - export
categories: [Study, React]
tags: [react, js, export]
---

# React Study

## export란
선언한 컴포넌트를 다른 모듈에서 사용할 수 있도록 내보낸다는 의미를 가지고 있다.

## 모듈 내보내기
#### named export
내보내길 원하는 변수나 함수, 클래스 앞에 export 키워드를 붙이는 것

#### 단일 컴포넌트 선언 시
React를 처음 세팅하고 나서 가장 처음 확인하는 App.js의 export 형태가 단일 컴포넌트를 선언했을 때 모듈을 내보내는 방법이다.
이 방식은 하나의 파일에 하나의 함수만 선언되어 있을 때 사용할 수 있다.

```react
function App(){
    return (
        <div>
            <h1>react<h1>
        </div>
    )
}

export default App;
```

```react
export default App;

function App(){
    ...
}
```
> 해당 모듈은 index.js에서 불러와 사용한다.
{: .prompt-tip }

동일한 내용을 다음의 형태로도 내보낼 수 있다. 위와 아래의 내보내기(export)는 동일하게 동작하며 export 키워드를 먼저 작성해도 올바르게 작성한다.

```react
export default function App(){
    ...
}
```


#### 복수 컴포넌트 선언 시
다음은 하나의 파일에 여러 개의 컴포넌트를 선언하고, 각각의 컴포넌트 선언부에 export를 붙여 여러 컴포넌트를 내보내는 방식이다.

```react
export function Test(){
    ...
}

export function Test1(){
    ...
}
```

각각의 컴포넌트 선언부에 키워드를 붙이는 것이 번거롭다면, 다음의 방식으로도 내보낼 수 있다.

```react
function Test1(){
    ...
}

function Test2(){
    ...
}

export {Test1, Test2};
```

### 주의점
모듈을 내보낼 때는 반드시 함수/변수/클래스 이름의 첫 글자가 대문자여야 하며, 모듈을 불러오고 내보낼 때 함수/변수/클래스명을 그대로 사용해야 한다.

#### 실패 예시
```react
export function test(){
    ...
}
```
> 첫 글자가 소문자 -> 불러오지 못함
{: .prompt-warning }

```react
// test.js
export function Test(){
    ...
}

// App.js
import hello from "./test";

function App(){
    <hello>Test() 호출<hello>
}
```
> test.js에서 export한 함수 -> Test / App.js에서 import한 함수 -> test.js의 hello
{: .prompt-warning }

필자는 이 부분을 모르고 소문자를 쓰거나 호출 시 이름을 바꿔서 불러오질 못했다.