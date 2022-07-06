---
layout: post
title: "react-router-dom v6"
categories:
  - react
tags:
  - react v18
  - react
  - router-dom v6
  - react-router-dom
---

react v18 업데이트 이후 React Router 또한 v6으로 업데이트 되었다  
v6에서 바뀐점이 많이 있지만 여기서는 나중에 사용 할 때 실수 하지 않기 위해   
자주 사용하던 기능 위주로 정리

## Switch -> Routes 변경 
---
`<Switch>`로 사용 했던 요소가 `<Routes>`로 변경 되었다.  
<br />
기존 코드 
```javascript
    <BrowserRouter>
        <Switch>
            <Route exact path="/"  component={TestPage} />
        </Switch>
    </BrowserRouter>
```

v6 변경된 코드
```javascript
    <BrowserRouter>
        <Routes>
            <Route path="/" element={<TestPage />}/>
        </Routes>
    </BrowserRouter>
```
### Switch -> Routes 변경되면서 바뀐점 
- 순서대로 매칭이 아닌 가장 일치하는 항목에 매칭
    - 잘못된 순서로 배치하여 발생하는 버그 방지

<br/>

## 경로 및 링크 
---

1. exact 사라지고 대신 정확히 매칭 되는 항목을 보여준다 
    - `*`사용하여 여러 경로를 매칭 하고 싶으면 경로 뒤에 /* 사용하여 매칭 
    ```javascript
        <Route path="example/*" element={<TestPage />}/>
    ```
2. `<Route children>` -> `<Route element>`로 변경 
    - component 또는 children대신 element props를 사용 

3. 단순화된 경로 형식 사용
    - 와일드 카드는 중간이 아닌 경로의 끝에서만 사용 가능
    - 유효한 경로
    ```
    /groups
    /groups/admin
    /users/:id
    /users/:id/messages
    /files/*
    /files/:id/*
    ```
    - RegExp 스타일 경로 경로는 v6에서 유효하지 않음
    ```
    /users/:id?
    /tweets/:id(\d+)
    /files/*/cat.jpg
    /files-*
    ```

## useHistory -> useNavigate 변경 
---
1. `<Redirect />` 없어지며 `<Navigate />`를 활용해야 한다
    - Redirect의 경우 replace가 기본 로직이지만(prop을 통해 push로 변경 가능)  
    Navigate의 경우 push가 기본 로직(prop을 통해 replace로 변경 가능)    
    - 기존 코드 
    ```javascript
    <Redirect  path ="*" to ="/" />
    ```
    - 변경된 코드
    ```javascript
    <Route path="*" element={<Navigate to="/" replace />}/>
    ```

이외에도 변경 사항이 많지만 아직은 공부중이며 직접 테스트 및 사용을 해보지 않아    
직접 사용해본 기능들만 정리 했으며 공부하는 대로 추가할 예정 

참고 사이트 : https://reactrouter.com/docs/en/v6/upgrading/v5#upgrade-to-react-router-v6
