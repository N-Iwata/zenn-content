---
title: "【JavaScript】ちょっとした配列操作tips"
emoji: "🀄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [JavaScript, 配列操作, map, reduce, カリー化]
published: true
---

## はじめに

データベース等から取得した配列データをいい感じに加工して違う配列にしたり、配列同士の積和を求めたい場合があると思います。

以下3点の配列操作のシュっとした書き方のtipsを書いていきます。

頭の体操程度に見ていただければと思います！！

## [1,2,3,4,5]を[0,1,3,6,10,15]にする

こちらはある配列データを折れ線グラフなどに使うデータに加工したい場合です。
最初は0からスタートして1番目は`0+1=1`、2番目は`1+2=3`のように積みあがっていく配列を加工します。
このような配列加工は累積和 (Cumulative sum) と言うそうです。

以下スクラップで意見募集し、@spdbearさんに素晴らしいアイデアを頂いたので今回はこちらを2つ採用させております。
自分が考えた酷い書き方もちょっと載せてます。。。
https://zenn.dev/rpf_nob/scraps/4eb43ab79755e9

### パターン1⃣

letで変数`temp`を新たに使い、`map`と`スプレッド演算子「...」`で配列を新たに作成する。
処理自体はわかりやすいけど、余計な変数が必要になってしまいます。。。

```js
const dataset = [1, 2, 3, 4, 5];
let temp = 0;
const result = [0, ...dataset.map((data) => temp += data)]
```

### パターン2⃣

関数のカリー化を使って、`map`と`スプレッド演算子「...」`で配列を新たに作成する。
アロー関数を使うと余計な変数を増やさず処理自体も1行で書けるのでシュっとした書き方ですね。
ただ、わかりにくい書き方なので自分のような初学者が最初にみると「うっ！？」となりそう。。。

```js
const dataset = [1, 2, 3, 4, 5];
const result = [0, ...dataset.map((sum => value => sum += value)(0))]
```

## [1,2,3,4.5]を[6.6,13.2,19.8,26.4.33.0]にする

次は配列データをそのまま比率の配列データに加工したい場合です。
四捨五入は割愛します。`Math.round`を使ったり、lodash使ったりしてください。

```js
const dataset = [1, 2, 3, 4, 5];
const sum = dataset.reduce((acc, cur) => acc + cur, 0);
const result = dataset.map((data) => (data / sum) * 100);
```

`sum`を作らずに直接`map`の中で計算すれば1行で書けそうですが、計算量増えるのでこれがいいかなと思います。

## [1,2,3,4.5]と[0.15, 0.25, 0.30, 0.10, 0.20]から2.95を求める

次は配列データ同士の積和です。
`1 × 0.15 + 2 × 0.25 + 3 × 0.30 + 4 × 0.10 + 5 × 0.20`のように配列の同じインデックス同士の積の和を求めます。

excelでいう`sumproduct`関数のことです。

`reduce`を使い、第3引数のindexを比率データに渡して`cur × percent_dataset[idx]`で同じindex同士の積になるので、`acc`に加算していけば実装できます。

```js
const base_dataset = [1, 2, 3, 4, 5]; // 元データ
const percent_dataset = [0.15, 0.25, 0.30, 0.10, 0.20]; // 比率データ
const result = base_dataset.reduce((acc, cur, idx) => acc + cur * percent_dataset[idx], 0)
```

ちなみに`[1,2,3,4,5]`のように必ず連番になっている場合は以下のようにも書けます。
平均順位を算出したいときとかはこちらのほうがシュっとしていますね。

```js
const percent_dataset = [0.15, 0.25, 0.30, 0.10, 0.20]; // 比率データ
const result = percent_dataset.reduce((acc, cur, idx) => acc + cur * (idx + 1), 0)
```

## 最後に

今回はちょっとした配列操作tipsを書きました。
他に思いついたら追記していきます！！

こっちのほうがシュっとした書き方じゃね？？とか間違っているよ！！とかありましたら、是非コメントお願いいたします！！


## 参考

https://stackoverflow.com/questions/20477177/creating-an-array-of-cumulative-sum-in-javascript/55261098

https://qiita.com/Yametaro/items/99cc1c8ebcfc703b1410