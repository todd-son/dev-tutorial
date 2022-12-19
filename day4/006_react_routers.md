# React Router
리액트를 하다보면 가장 필요한 기능중의 하나가 라우팅 기능이다. 이에 가장 유명한 라우터 라이브러리를 사용해보겠다.

## 라이브러리 설치

```
npm install --save react-router-dom
```

package.json에 잘 설치 된것을 확인할 수 있다.

```json
{
    "name":"tutorial-app",
    "version":"0.1.0",
    "private":true,
    "dependencies":{
      "@emotion/react":"11.10.5",
      "@emotion/styled":"11.10.5",
      "@mui/material":"5.11.0",
      "@testing-library/jest-dom":"5.16.5",
      "@testing-library/react":"13.4.0",
      "@testing-library/user-event":"13.5.0",
      "react":"18.2.0", "react-dom":"18.2.0",
      "react-router-dom":"6.5.0",
      "react-scripts":"5.0.1",
      "web-vitals":"2.1.4"
    }
}

```

## 라우터 선언

아래와 같이 Routes 하위에 경로와 이동할 대상이 되는 컴포넌트를 지정한다.

```javascript
function App() {
    return (
        <div>
            <BrowserRouter>
                <Grid container spacing={2}>
                    <Grid item xs={2}>
                        <SideMenu/>
                    </Grid>
                    <Grid item xs={10}>
                        <Routes>
                            <Route path="/" element={<Main/>}></Route>
                            <Route path="/member" element={<MemberRoot/>}></Route>
                            <Route path="/course" element={<CourseRoot/>}></Route>
                        </Routes>
                    </Grid>
                </Grid>
            </BrowserRouter>
        </div>
    );
}

export default App;
```

## 라우터 동작

각 컴포넌트에 아래와 같이 링크를 걸어서 간단하게 해당 컴포넌트로 이동을 구현할 수 있다.

```javascript
class SideMenu extends React.Component {
    render() {
        return (
            <div style={{textDecoration: 'none'}}>
                <MenuList>
                    <MenuItem component={Link} to={'/member'}>
                        <ListItemText>Member</ListItemText>
                    </MenuItem>
                    <MenuItem component={Link} to={'/course'}>
                        <ListItemText>Course</ListItemText>
                    </MenuItem>
                </MenuList>
            </div>
        )
    }
}

export default SideMenu;
```