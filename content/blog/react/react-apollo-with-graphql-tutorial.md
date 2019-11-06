---
title: react apollo with graphql tutorial
date: 2019-11-07 00:11:84
category: react
---

repository : https://github.com/net-lmcd/apollo-graphql-tutorial

React Apollo client에서 GraphQL를 사용해보는 튜토리얼 입니다. 프로젝트는 CRA기반 으로 구성하였고 graphql endpoint는 https://api.github.com/graphql 입니다.

### 1-1. Client 생성

```js
//index.js
import ApolloClient from 'apollo-boost'

const client = new ApolloClient({
  uri : 'graphql endpoint uri',
  request : operation => {
    operation.setContext({
      headers : {
        authorization : `Bearer token` // token 필요한 경우
      }
    })
  }
})
```

- uri : 우리가 data를 fetch할 graphql endpoint uri
- request : 위와 같이 코딩할 경우 모든 request에서 헤더에 토큰을 실어 보낸다.

<br/>

### 1-2. React에 Client 연동

```js
//index.js
import {ApolloProvider } from 'react-apollo'

...//1-1 코드

render(
	<ApolloProvider client={client}>
  	<App/>
  </ApolloProvider>,
  document.getElementById('root')
)
```

- ApolloProvider 컴포넌트로 최상위 컴포넌트를 감싸주게 되면 react-context provider와 비슷하게 어떤 컴포넌트든지 client에 접근할 수 있게된다.

<br/>

### 2-1.  컴포넌트에서 Data Fetch하기

```js
//App.js

import React from 'react'
import { Query } from 'react-apollo'
import { gql } from 'apollo-boost'

const query = gql`
 {
	viewer{
 		name
		email
  }
 }
`

const App = () => {
  return(
  	<div className="App">
      <Query query={query}>
      	{ result => {
          if (result.loading) return <p>loading...</p>
          if (result.error) return <p>{result.error.message}</p>
          else{
            return(
            	<div>
              	<h1>{result.data.viewer.name}</h1>
                <p>{result.data.viewer.email}</p>
              </div>
            )
          }
         }
        }
      </Query>
    </div>
  )
}
```

- <code>react-apollo</code> 모듈에서는 Query컴포넌트를 제공해주는데 query라는 변수를 props로 받고, 우리는 이 query를 정의할 수 있다.  ( 여기서 잠깐, GraphQL에서 Query가 의미하는건 API로부터 data를 가져오는 것 이고, Mutaion이 의미하는 것은 data를 지우거나 업데이트 한다는 의미이다.)

- Query컴포넌트에 의해 나오는 결과값은 3가지 상태를 가지고 있다 ( loading, error, data )

  loading은 request가 진행중일 경우, error는 request가 실패한 경우, data는 query의 결과값 이다.

<br/>

### 2-2. 쿼리에 변수 만들기

```js
//MyRepositories.js

import React from 'react'
import { Query } from 'react-apollo'
import { gql } from 'apollo-boost'

const reposQuery = gql`
 query MyRepositories($first : Int!){
   viewer{
    repositories(first : $first){
      edges{
        node {
          name
        }
      }
    }
  }
 }
`

const MyRepositories = () => {
  return(
      <Query query={reposQuery} variables={{first : 10}}>
        {( {loading, error, data}) => {
            if (loading) return <p>loading...</p>
            if (error) return <p>{error.message}</p>
            return(
                <ul>
                  <h2>Top 10 repositories</h2>
                  {data.viewer.repositories.edges.map(({node},index) => {
                      return(
                          <li key={index}>{node.name}</li>
                      )
                    })
                  }
                </ul>
            )
          }
        }
      </Query>
  )
}

export default MyRepositories
```

- 쿼리에 변수를 주고 싶다면 $ 를 사용하면 된다.
- Query 컴포넌트에서 variables props로 변수를 전달해주면 된다.

<br/>

### 2-3 More Fetch

```js
import React from 'react'
import { Query } from 'react-apollo'
import { gql } from 'apollo-boost'

const reposQuery = gql`
 query MyRepositories($first : Int!){
   viewer{
    repositories(first : $first){
      edges{
        node {
          name
        }
      }
    }
  }
 }
`

const MyRepositories = () => {
  const handleMore = (current, fetchMore, data) => {
    fetchMore({
      variables : {first : current + 3},
      updateQuery : (prev, {fetchMoreResult}) => {
        if(!fetchMoreResult) return prev
        else {
         return Object.assign({}, prev, fetchMoreResult)
        }
      }
    })
  }
  return(
      <Query query={reposQuery} variables={{first : 6}}>
        {({loading, error, data, fetchMore}) => {
          if (loading) return <p>loading...</p>
          if (error) return <p>{error.message}</p>
          let current = data.viewer.repositories.edges.length
          return(
                <div>
                  <ul>
                    <h2>First {current} repositories</h2>
                    {data.viewer.repositories.edges.map(({node},index) => {
                      return(
                          <li key={index}>{node.name}</li>
                      )
                    })
                    }
                  </ul>
                  <button onClick={()=>handleMore(current, fetchMore, data)}>Fetch More</button>
                </div>
            )
          }
        }
      </Query>
  )
}

export default MyRepositories
```

더 많은 data를 fetch하기 위해서는 Query 컴포넌트가 제공해주는 fetchMore 함수를 이용하면 된다.

fetchMore 함수의 인자로는 variables, updateQuery가 있다. 먼저 variables는 GraphQL query에 전달한 쿼리변수를 의미하고, updateQuery는 이전 data와 update된 후 data를 합치는 기능(Merge)을 하고 컴포넌트를 re-rendering 시킨다.

<br/>

### 3. Mutation

```js
import React, { Component } from "react";
import { Mutation } from "react-apollo";
import { gql } from "apollo-boost";

const addStarquery = gql`
  mutation AddStar($repoid: ID!) {
    addStar(input: { starrableId: $repoid }) {
      starrable {
        stargazers {
          totalCount
        }
        viewerHasStarred
      }
    }
  }
`;

class AddStar extends Component {
  render() {
    return (
      <Mutation mutation={addStarquery}>
        {(addStar, { data, loading, error }) => {
          return (
            <div>
              <button
                onClick={() => {
                  addStar({ variables: { repoid: this.props.id } }).then(
                    res => {
                      this.props.refetch();
                    }
                  );
                }}
              >
                {" "}
                Addstar
              </button>

              {loading && <p>loading...</p>}
              {error && <p>{error.message}</p>}
            </div>
          );
        }}
      </Mutation>
    );
  }
}

export default AddStar;
```

- Query 컴포넌트는 백엔드 API로 부터 데이터를 가져오기만 가능하다, 데이터를 업데이트 하거나 삭제 하기 위해서는 Matation 컴포넌트를 사용해야 한다.
- Mutaion이 한번 완료되면 백엔드에서 데이터를 다시 반환한다.
- Matation 컴포넌트 내부 함수의 첫번째 파라미터로는 Mutaion을 일으키는 함수, 두번째 파라미터로는 백엔드로 부터 받은 객체이다 ( data를 포함한 )
- Mutaion이 완료되면 <code>refetch()</code> 메소드를 통해 UI를 업데이트 할 수 있다. 