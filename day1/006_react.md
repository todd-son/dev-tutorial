# React 시작하기

## React 설치

node가 정상적으로 설치되었으면 아래의 명령어로 기본 템플릿을 생성할 수 있다.

```shell
npx create-react-app tutorial-app
```

## 첫번째 컴포넌트

Component 클래스를 상속받아서 render() 함수를 구현하자. <html>을 리턴함으로써 화면을 표시할 수 있다.

```javascript
import {Component} from "react";

class Hello extends Component {
    render() {
        return (
            <div>Hello world</div>
        )
    }
}

export default Hello
```

만들어진 컴포넌트는 import 시킨 다음 tag형태로 참조할 수 있다. App function은 리액트 화면의 시작점이다.

```javascript
import './App.css';
import React from "react";
import Hello from "./Hello";


function App() {
    return (
        <div>
            <Hello></Hello>
        </div>
    );
}

export default App;
```

## SPA란?

SPA(Single Page Application)이다. 이름에서도 파악할 수 있듯이, 어떤 웹 사이트의 전체 페이지를 하나의 페이지에 담아 동적으로 화면을 바꿔가며 표현하는 것이 SPA이다. 뭔가를 클릭하거나 스크롤하면, 상호작용하기 위한 최소한의 요소만 변경이 일어난다. 페이지 변경이 일어난다고 보여지는 것 또한 최초 로드된 자바스크립트를 통해 미리 브라우저에 올라간 템플릿만 교체되는 것이다.

[https://www.huskyhoochu.com/what-is-spa/](https://www.huskyhoochu.com/what-is-spa/)


## CORS란?

브라우저에서는 보안적인 이유로 cross-origin HTTP 요청들을 제한합니다. 그래서 cross-origin 요청을 하려면 서버의 동의가 필요합니다. 만약 서버가 동의한다면 브라우저에서는 요청을 허락하고, 동의하지 않는다면 브라우저에서 거절합니다. 이러한 허락을 구하고 거절하는 메커니즘을 HTTP-header를 이용해서 가능한데, 이를 CORS(Cross-Origin Resource Sharing)라고 부릅니다.

[https://hannut91.github.io/blogs/infra/cors://hannut91.github.io/blogs/infra/cors](https://hannut91.github.io/blogs/infra/cors://hannut91.github.io/blogs/infra/cors)


## API 연동 및 데이터 다루기

http api 호출은 fetch라는 메소드를 활용한다. CORS문제 해결을 위해 package.json 에 프록시 설정을 하자.

```
"proxy": "http://localhost:8080"
```

```javascript
        fetch('/api/members')
            .then((response) => response.json())
            .then(memberList => {
                this.setState({members: memberList});
            });
```

state가 중요하다. state가 변경되면, 컴포넌트는 리렌더링됩니다. state를 변경할때는 setState 메소드를 활용해서 업데이트를 해야 된다.

```javascript
 state = {
        members: []
    }
```

그리고 리액트에서는 다양한 훅들을 제공하는데 화면이 컴포넌트가 로딩이 완료된 이후 특정 작업을 넣는다면 componentDidMount() 메소드를 구현하면 된다.

```javascript
import React, {Component} from 'react';

class Member extends Component {
    state = {
        members: []
    }

    fetchMembers() {
        fetch('/api/members')
            .then((response) => response.json())
            .then(memberList => {
                this.setState({members: memberList});
            });
    }

    componentDidMount() {
        this.fetchMembers()
    }

    render() {
        return (
            <div>
                <ul>
                    {this.state.members.map((member) => {
                        const display = member.name + "(" + member.id + ")"
                        return (
                            <li key={member.id}>{display}</li>
                        )
                    })}
                </ul>
            </div>
        )
    }
}

export default Member;
```

## Material UI 설치하고 화면을 이쁘게

```shell
npm install @mui/material @emotion/react @emotion/styled --save
```

```javascript
    render() {
        return (
            <Box sx={{width: '100%', maxWidth: 360, bgcolor: 'background.paper'}}>
                <nav aria-label="secondary mailbox folders">
                <List>
                    {this.props.members.map((member) => {
                            const display = member.name + "(" + member.id + ")"
                            return (
                                <ListItem key={member.id}>
                                    <ListItemText primary={display}/>
                                </ListItem>
                            )
                        }
                    )}
                </List>
                </nav>
            </Box>
        )
    }
```




