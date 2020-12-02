---
title: "【JavaScript】findメソッドの危険性についての考察"
emoji: "🀄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [JavaScript, find, filter]
published: true
---

## はじめに

最近JavaScriptのfindメソッドを使った時に、急に怖くなったので記事にしてみたいと思いました。

## findメソッドとは❓

ES6(ES2015)で追加されたメソッドでMDNでは以下のように説明されています。

> 提供されたテスト関数を満たす配列内の 最初の要素 の 値 を返します。

簡単に言うと、配列の中から上から順番に検索して、**最初に条件に合致する**ものを返すということになります。

https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/find


MDNにはこのような例文が記載されています。
```js
const array1 = [5, 12, 8, 130, 44];
const found = array1.find(element => element > 10);
console.log(found);
// expected output: 12
```
この例だと`array1`を順番に検索して行って、10より大きい値があったらそれを返すので、`12`が出力されます。
まあこの辺はJavaScript書く方なら皆さん知っている内容かと思います。

## 何が怖くなったのか❓

実際に使用するときの例としては、以下のような配列から`id`が合致するものを検索して`name`を取り出したい場合があると思います。

次のコードは`id`が`001`のデータを`dataset`から取得して`name`を取り出しています。

```js
const dataset = [
  { id: "001", name: "aaa" },
  { id: "002", name: "bbb" },
  { id: "003", name: "ccc" },
  //・・・
];
const id = "001"
const resFind = dataset.find((data) => data.id === id);
console.log(resFind.name);
// expected output: aaa
```

うん。普通に`id`が`001`のデータから`name`が取り出せてますね。
しかし、以下のように`dataset`に実はもう一つ`id`=`001`があった場合には、

```js
const dataset = [
  { id: "001", name: "aaa" },
  { id: "002", name: "bbb" },
  { id: "003", name: "ccc" },
  //・・・
  { id: "001", name: "zzz" }, // さらにこういうデータがあった場合
];
```

次のように何事もなく`aaa`が取得できます。

```js
const id = "001"
const resFind = dataset.find((data) => data.id === id);
console.log(resFind.name);
// expected output: aaa
```

この**何事もなく取得できてしまう**のって怖くないですか？？
私はこの部分にfindメソッドを使う怖さを感じました。

よくあるユースケースとして、データベースからユーザー情報を持ってきて、IDで名前や年齢などの情報を取得することが多いと思います。基本的にはIDは一意の値にしていると思いますが、実は一意になってなかった場合に、この辺りの情報を間違えて取得することがあるし、それに**気づくことができない**可能性があります。

なので、個人的にはfindは使うのが怖いので使わないようにしようかと思っています。

## なのでfilter使う

じゃあどうしようかという問題が出てくるけど、`filter`で代用が可能です。

MDNでは以下のように説明されています。
>与えられた関数によって実装されたテストに合格したすべての配列からなる新しい配列を生成します。

https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Array/filter

`find`メソッドと違うのは条件に合致しているものを全て取得して配列にしてくれます。
以下の例だと`id`が`001`のものが二つあるので、二つを取得して配列に格納してくれます。
```js
const dataset = [
  { id: "001", name: "aaa" },
  { id: "002", name: "bbb" },
  { id: "003", name: "ccc" },
  //・・・
  { id: "001", name: "zzz" },
];

const id = "001"
const resFilter = dataset.filter((data) => data.id === id);
console.log(resFind);
// expected output: [{id: "001", name: "aaa},{id: "001", name: "zzz}]
```

ここで`find`メソッドとは違って条件に合致したものを全て配列に格納してくれるので、`length`が`1`であれば一意のデータが取得できているということになります。
ですので、`length`が1ではなかったらログに出したり、アラート出したりすれば気づけると思います。
```js
if (resFilter.length === 1) {
  console.log(resFilter[0].name);
}
else{
  console.log("multiple same id.");
}
```
こうすれば安心ですよね！！

:::message
コメントから追記（rithmetyさんありがとうございます！！）
:::

`productionモード`では無駄な処理をさせないことも重要なので、以下のように`developモード`だけ`filter`を使ったチェックを行うのが良さげです。その場合はそのまま`find`で取得しても問題なさそうです。
```js
if ("development" === process.env.NODE_ENV) {
  if (1 !== dataset.filter((data) => data.id === id).length) {
    console.log("multiple same id.")
    process.exit(1)
  }
}
```

## まとめ

今回は`find`メソッド危険性についての考察と題して、実際にコード書くときに使って見たら怖いなと思ったので記事にしてみました。初学者レベルなので他にもっといいやりかたなどがあれば教えて欲しいと思ったというのも記事にした理由になります。

間違っていたり、もっとこうしたほうがいいよとかありましたら、是非コメントいただければと思います！

最後までお読みいただきありがとうございました。

