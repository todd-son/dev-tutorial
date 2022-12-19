# React

JPA 실습으로 만든 화면을 리액트와 통합해보자.

## Pagination 적용

기존 멤버리스트 컴포넌트 하위에 Pagination 컴포넌트를 추가한다.

```javascript
render() {
    return (
        <Box sx={{flexGrow: 1}}>
            <Grid container spacing={2}>
                <Grid item xs={12}>
                    <MemberList members={this.state.members} page={this.state.page} name={this.state.memberName}/>
                </Grid>
                <Grid item xs={12}>
                    <Pagination count={10} page={this.state.page} onChange={this.handlePageChange}/>
                </Grid>
            </Grid>
        </Box>
    )
}
```

페이지가 변경될 경우 처리 함수에서 state을 변경시켜주고 다시 패치를 수행하도록 한다.

```javascript
handlePageChange = (event, value) => { 
    this.fetchMembers(value);
};

fetchMembers(page) {
    let url = `/api/v5/members?page=${page}`;

    fetch(url)
        .then((response) => response.json())
        .then(memberList => {
            this.setState({page: page})
            this.setState({members: memberList});
        });
}
```

## 검색 기능 적용

검색 기능을 위해 TextField와 버튼을 추가해보자.

```javascript
render() {
    return (
        <Box sx={{flexGrow: 1}}>
            <Grid container spacing={2}>
                <Grid item xs={12}>
                    <Box display="flex" justifyContent="flex-end">
                        <TextField id="outlined-basic" label="Name" variant="outlined"
                                   onChange={(event) => this.searchCondition.memberName = event.target.value}
                                   InputProps={{
                                       endAdornment: <Button variant="contained"
                                                             onClick={this.handleSearchClick}>Search</Button>
                                   }}
                        />
                    </Box>
                </Grid>
                <Grid item xs={12}>
                    <MemberList members={this.state.members} page={this.state.page} name={this.state.memberName}/>
                </Grid>
                <Grid item xs={12}>
                    <Pagination count={10} page={this.state.page} onChange={this.handlePageChange}/>
                </Grid>
            </Grid>
        </Box>
    )
}
```

핸들러 메소드와 기존 fetch 메소드를 수정해서 검색 기능을 추가해보자.

```javascript
    handleSearchClick() {
        this.fetchMembers(1);
    }

    fetchMembers(page) {
        let url = `/api/v5/members?page=${page}`;

        if (this.searchCondition.memberName !== undefined) {
            url = `${url}&name=${this.searchCondition.memberName}`;
        }

        fetch(url)
            .then((response) => response.json())
            .then(memberList => {
                this.setState({page: page})
                this.setState({members: memberList});
            });
    }
```