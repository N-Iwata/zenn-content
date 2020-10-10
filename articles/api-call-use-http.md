---
title: "use-httpというAPIコールのライブラリが良さそうなので試してみた"
emoji: "💻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [React, useHttp, API, GraphQL]
published: true
---

## はじめに

この記事は[Developer Roadmaps](https://roadmap.sh/react)で2020年のReactロードマップの中で作者がおススメしている（前に2020年のを見た時なかったような気がする・・・？）、`use-http`というHooksベースで実装されているAPIコールのパッケージを調べてみたら良さそうだったので試してみたまとめです。


[公式ドキュメントはこちら](https://use-http.com/)
[公式のGitHubのリポジトリはこちら](https://github.com/ava/use-http)
[CodeSandboxでちょっと試したコードはこちら](https://codesandbox.io/s/react-use-http-92k4l?file=/src/UserList2.jsx:0-523)

## 何ができるの❓

RestfulAPIやGraphQLのAPIリクエストがこれ一つでできるパッケージです。
言うなれば`axios`と`Apollo Client`の機能が両方使えるようなものです。（たぶん）

## パッケージインストール

```
$npm install use-http --save
```

## 基本的な使用法

まずは基本的な使用法で、[JSONPlaceholder](https://jsonplaceholder.typicode)から`users`データをとってきて`username`を表示してみます。

`useFetch`の最後に渡す`[]`でマウント時に実行されるようになります。

```js
import React from "react";
import useFetch from "use-http";

const UserList = () => {
  const options = {};
  const { loading, error, data = [] } = useFetch(
    "https://jsonplaceholder.typicode.com/users", options, []
  );

  return (
    <>
      <h2>UserList</h2>
      {error && "Error!"}
      {loading && "Loading..."}
      {data.map((user) => (
        <div key={user.id}>{user.id}:{user.username}</div>
      ))}
    </>
  );
};
export default UserList;
```

## ステートで管理したい場合

`useState`などで状態を管理したいときは、`useEffect`でマウントされた時に`get`を使ってデータを取得しにいきます。
`await get("/users");`でデータ取得できます。

```js
import React, { useState, useEffect, useCallback } from "react";
import useFetch from "use-http";

const UserList = () => {
  const [users, setUsers] = useState([]);
  const { get, response, loading, error } = useFetch(
    "https://jsonplaceholder.typicode.com"
  );

  const initializeUsers = useCallback(async () => {
    const initialUsers = await get("/users");
    if (response.ok) setUsers(initialUsers);
  }, [get, response]);

  useEffect(() => {
    initializeUsers();
  }, [initializeUsers]); // componentDidMount

  return (
    <>
      <h2>UserList1</h2>
      {error && "Error!"}
      {loading && "Loading..."}
      {users.map((user) => (
        <div key={user.id}>
          {user.id}:{user.username}
        </div>
      ))}
    </>
  );
};
export default UserList;
```

## ページネーション

今度はTodoをページネーションで取得したい場合です。
ページ番号をステートで管理して、欲しいページのTodoデータを取得しています。
`onNewData`のところで、現在のTodoデータに新しいTodoデータを連結しています。
普通に次のページの分だけ表示する場合は`onNewData: (currTodos, newTodos) => newTodos,`のようにそのまま新しい取得データにしてやればいけます。

```js
import React, { useState } from "react";
import useFetch from "use-http";

const TodoList = () => {
  const [page, setPage] = useState(1);
  const perPage = 10;
  const { loading, data: todos } = useFetch(
    `/todos?_start=${(page - 1) * perPage}&_limit=${perPage}`,
    {
      onNewData: (currTodos, newTodos) => [...currTodos, ...newTodos],
      data: []
    },
    [page]
  );
  return (
    <>
      <h2>TodoList</h2>
      {todos.map((todo, index) => (
        <div key={index}>
          {todo.id}:{todo.title}
        </div>
      ))}
      {loading && "Loading..."}
      {!loading && (
        <button onClick={() => setPage(page + 1)}>Load More Todos</button>
      )}
    </>
  );
};
export default TodoList;
```
呼び元の`App.jsx`
```js
import React from "react";
import { Provider } from "use-http";
import TodoList from "./TodoList";

export default function App() {
  const options = {
    headers: {
      Accept: `application/json`
    }
  };

  return (
    <div className="App">
      <Provider options={options} url="https://jsonplaceholder.typicode.com">
        <TodoList />
      </Provider>
    </div>
  );
}
```

## GraphQL Query

GrapuQLのQueryも`useFetch`を使ってリクエストできます。
以下はドキュメント原文ままです。

一旦queryを定義しておいて、`useFetch`を使用して、GraphQLのQueryでリクエストできます。

```js
const QUERY = `
  query Todos($userID string!) {
    todos(userID: $userID) {
      id
      title
    }
  }
`
function App() {
  const request = useFetch('http://example.com')
  const getTodosForUser = id => request.query(QUERY, { userID: id })

  return (
    <>
      <button onClick={() => getTodosForUser('theUsersID')}>Get User's Todos</button>
      {request.loading ? 'Loading...' : <pre>{request.data}</pre>}
    </>
  )
}
```

## GraphQL Mutation

GrapuQLのMutationも`useFetch`を使ってリクエストできます。
以下はドキュメント原文ままです。

一旦Mutationを定義しておいて、`useFetch`を使用して、GraphQLのMutationでリクエストできます。
```js
const MUTATION = `
  mutation CreateTodo($todoTitle string) {
    todo(title: $todoTitle) {
      id
      title
    }
  }
`
function App() {
  const [todoTitle, setTodoTitle] = useState('')
  const request = useFetch('http://example.com')
  const createtodo = () => request.mutate(MUTATION, { todoTitle })

  return (
    <>
      <input onChange={e => setTodoTitle(e.target.value)} />
      <button onClick={createTodo}>Create Todo</button>
      {request.loading ? 'Loading...' : <pre>{request.data}</pre>}
    </>
  )
}
```

## まとめ

他にも様々な機能があるので、公式ドキュメントを参照してみてください。
個人的にはApollo Clientを勉強中だったので、こちらもしっかり勉強する必要があると思いました。