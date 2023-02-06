---
title: "Next.js×TypeScript×microCMS×MantineUI×Netlifyでブログを作成したのでまとめと感想"
emoji: "🌱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [react, typescript, mantine, microcms, netlify]
published: true
---

## はじめに

この記事は、タイトルに記載の通りの技術スタックで趣味の芝生管理用のブログを作成したので、簡単にまとめと感想を書いておこうと思います。

実際のブログはこちら
https://shibaflog.com/

GitHubリポジトリはこちら（参考になった方は🌟をポチッと🙏）
https://github.com/N-Iwata/shibaflog

## ブログ作成の動機

普段業務でNextjs(React)を触っているがSSRする必要がないアプリケーションということもあり、ファイルルーティングの機能くらいしか触っていなかったため、SSGやSSRの機能をちょっと触っておきたかったのと、最近流行りのJAMStack構成をmicroCMSを用いて経験しておきたかったという2点が主な動機です。

あと、勤めている会社でホームページを作り直す話があがっていたので、JAMStack構成を提案するために素振りをしたかったというのも大きな動機です。

技術ブログ自体はReact初学者の頃に練習としてGatsbyで作成したので、今回は趣味の芝生管理用で作成しようと思い、開発に着手しました。

使用したものを以下の通り、まとめていきたいと思います。

- Next.js
- TypeScript
- microCMS
- MantineUI
- Chromatic
- Netlify

## Next.js

上にも書いたが、そもそもの動機がNext.jsでmicroCMSを用いたJAMStack構成を触っておきたいということなので、Next.jsを使用しました。

他の選択肢だと、Netlifyに買収されたGatsbyがまた盛り返してくるかどうかや、最近v2になってさらに話題になってるAstroとかが選択肢にあがってくるんじゃないかと思いますが、とりあえずNext.jsで。

## TypeScript

個人的にはマストで。（any1箇所使ってるくせに）

## microCMS

こちらもそもそもの動機がNext.jsでmicroCMSを用いたJAMStack構成を触っておきたいということなので、microCMSを使用しました。

https://microcms.io/

他のヘッドレスCMSをあまり知らないので他にもあるかもしれませんが、自社HPで使用するかもという前提で、日本語対応、下書き、予約、レビューなどのキーワードがクリアしているので、microCMSを選択しました。

あと、microCMSの中の人たちのTech Blogにほとんどのブログとしての機能の実装方法が紹介されているのも大変助かりました。

## MantineUI

UIライブラリはMantineUIを使用しました。

https://mantine.dev/

普段業務ではMaterialUIを使用していますが、他のUIライブラリを使おうかなと考えていたところ、以下の記事を発見して良さそうだと思い使用してみました。

https://zenn.dev/thr3a/articles/2c35b5d75081d7

MUIではv5からsx propsでインラインでのスタイリングできるようになりましたが、MantineUIも同じようにsx propsでのスタイリングができるので、学習コストが個人的に高くないというのも良さそうだと思いました。

また、ドキュメントも充実してるし、Discord内でのやりとりも活発で、これから広く使われるのではないかと思います。有名なReact Roadmapの2023版に追加されましたしね。

https://roadmap.sh/react

あと、個人的には便利なhooksやform周りのlibraryもあるので、こちらも魅力的かと思います。（コード読んでるだけで勉強になります）

https://mantine.dev/hooks/use-counter/

## Chromatic

storybookはchromaticで管理するようにしました。

今回はホスティングくらいの目的で使用していますが、UIレビューやUIテストにも使用できるので、業務でも使えそうな雰囲気です。

https://www.chromatic.com/

## Netlify

ホスティング先はNetlifyにしました。

無料プランを前提にすると、この辺りの記事からVercelのほうが速いという情報だけは持っていました。

https://qiita.com/fussy113/items/ba204747e3f0e6c59af0

ただ、実際にlighthouseでPerformanceを比較すると、意外とNetlifyのほうがスコアが良かったです。（これで測るのが正解じゃないかもしれませんが、上がNetlifyで下がVercelにホスティングした場合のスコアです。）

![](/images/next-ts-microcms-matine-blog/image01.png)

ビルド時間は、Vercelだと57sでNetlifyだと1m37sでしたので、コンテンツ量が増えていったら気にならんレベルかなと思いました。

あと、せっかくブログ作るということで、モチベーションのためにも、アドセンスくらいは入れてドメイン代＋αくらいは稼ぎたいという思い（なんだかんだこれが一番優先度高い）もあったので、Netlifyにしました。
（Vercelだとこの辺りで明確に無料プランでは商用利用になると書いてある。）

https://vercel.com/docs/concepts/limits/fair-use-policy


## まとめ

microCMSを使用したJAMStack構成についてはweb制作の業務や機会があれば、おそらく今後も使用したいし提案もするかと思います。

今後の課題としては、現在のmicroCMSのリッチエディタでは、リンクの埋め込みが特定のサイトしか対応していないようなので、ビルド時にmetaタグのtitleやogpのurlなどを取得していい感じのリンクカード(?)のコンポーネントに置き換えるのを追加したりしようかと思います。リッチエディタv2が開発中のようなので、この辺はちょっと期待したいところです。

他には、芝生管理のブログということで、ブログ投稿でGitHubのように草が生えるようにしてみたりしました😀 せっかくなので、しっかり草が生えるように実際に芝生管理を行なって、記事も投稿していきたいと思います。（react-calendar-heatmapを使用しました。）

![](/images/next-ts-microcms-matine-blog/image02.png)


最後まで読んでいただきありがとうございました！
間違っている点やもっとこうした方がいいよとかありましたら、ぜひコメントお願いします🙏