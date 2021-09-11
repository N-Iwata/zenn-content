---
title: "【VSCode Extension】JavaScriptのconsoleを簡単に挿入できる拡張機能を作って公開してみた"
emoji: "🀄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [VSCode, TypeScript, JavaScript]
published: true
---

## はじめに

この記事は以下を目的としています。

* VSCodeの拡張機能を作って公開する方法の簡単な説明
* 作った拡張機能の宣伝

## どういうものを作ったのか❓

JavaScript(TypeScript)でconsole.logなどの構文を簡単に挿入できるものを作りました。今まで使用していたほぼ同じような拡張機能があり、ちょっと機能追加したかったのですが、しばらく更新されていないようだったので自身で作り直して公開してみることにしました。

ここからインストールできますのでよかったらインストールして試してみてください🙇‍♂️

https://marketplace.visualstudio.com/items?itemName=N-Iwata.vscode-extention-js-console

## 何ができるのか❓

#### 変数などをVSCode上で選択又はカーソルを乗せた状態の場合

* Cmd+Shift+L => console.log('variable: ', variable);
* Cmd+Shift+T => console.table('variable: ', variable);
* Cmd+Shift+W => console.warn('variable: ', variable);
* Cmd+Shift+E => console.error('variable: ', variable);

もともと使っていた拡張機能は変数を選択する必要があったが、カーソルを変数に乗せただけで挿入できるようにしたかったのでこのような機能になっています。

(windowsはCmd→Ctrl)

#### 変数などをVSCode上で選択していない状態の場合（空行など）

* Cmd+Shift+L => console.log();
* Cmd+Shift+T => console.table();
* Cmd+Shift+W => console.warn();
* Cmd+Shift+E => console.error();

(windowsはCmd→Ctrl)

#### 動作イメージ

![](https://storage.googleapis.com/zenn-user-upload/2fa2d44ddd43e47197530e2a.gif)

他にもconsoleオブジェクトのメソッドがたくさんあるので今後追加しようかと考えています。

https://developer.mozilla.org/ja/docs/Web/API/console

## 拡張機能の作り方

ここから簡単に拡張機能の作り方を説明していきます。

基本的に公式のドキュメントを読みながらやるのが一番いいかと思います。

https://code.visualstudio.com/api

Node.jsとGitが必要なのでなければ先にインストールしてください。
ちなみに筆者はM1チップのmacbook proで開発しましたが何も問題なかったので安心して開発できると思います。

### プロジェクトを作る

詳細は以下ドキュメントを参照してください。
https://code.visualstudio.com/api/get-started/your-first-extension

#### パッケージのインストール

まず [Yeoman](https://yeoman.io/)というパッケージと[VS Code Extension Generator](https://www.npmjs.com/package/generator-code)というパッケージをインストールします。

グローバルに入れたくない方はローカルのプロジェクトでも問題ないと思います。私はそのままグローバルに入れました。

```
npm install -g yo generator-code
```

node.jsをnodenvで入れている方はrehashすればyoコマンドが使用できるようになります。

```
nodenv rehash
```

#### プロジェクトを作る

この状態で以下コマンドを実行するといろいろ初期設定を聞かれるので選択していきます。
名前とかは後でpackage.jsonで書き換えられるのでだいたい決めればいいと思います。

```
yo code

# ? What type of extension do you want to create? New Extension (TypeScript)
# ? What's the name of your extension? HelloWorld
### Press <Enter> to choose default for all options below ###

# ? What's the identifier of your extension? helloworld
# ? What's the description of your extension? LEAVE BLANK
# ? Initialize a git repository? Yes
# ? Bundle the source code with webpack? No
# ? Which package manager to use? npm
```

すると以下のようなフォルダ構成でプロジェクトが作成されます。

![](https://storage.googleapis.com/zenn-user-upload/7fe116c54bda52cba5790676.png)

#### デバッグ実行する

とりあえずF5を押してでデバッグ実行してみると、新しいVSCodeのウインドウが開かれるので[shift + cmd + p]でコマンドパレットからHello Worldが実行できます。

### 拡張機能を開発する

基本的にsrc/extension.tsとpackage.jsonの二つのファイルを編集していきます。

さまざまな機能の拡張機能を開発することができるので、詳細は以下ドキュメントを参照してください。

https://code.visualstudio.com/api/extension-guides/overview

今回の拡張機能のソースコードはこちらにあるので参考にしてみてください。

https://github.com/N-Iwata/vscode-extension-js-console


### 拡張機能を公開する

詳細は以下ドキュメントを参照してください。
https://code.visualstudio.com/api/working-with-extensions/publishing-extension

#### パッケージインストール

まず[vsce](https://github.com/microsoft/vscode-vsce)というパッケージをインストールしておきます。
拡張機能をパッケージ化したり、公開したりするコマンドが使えるようになります。

```
npm install -g vsce
```

node.jsをnodenvで入れている方はrehashすればvsceコマンドが使用できるようになります。

```
nodenv rehash
```

#### Azure DevOpsでPersonal Access Tokensを取得

VSCodeの拡張機能を公開するには[Azure DevOps](https://dev.azure.com)でPersonal Access Tokensを作る必要があります。MicroSoftアカウントが必要なのでなければ作成しましょう。

アカウントを作成しログインしたら、以下のようにPersonal Access Tokensを作成してください。（公式ドキュメントから画像拝借）

![](https://storage.googleapis.com/zenn-user-upload/3c02b17eddc207c3dce5783d.png)

![](https://storage.googleapis.com/zenn-user-upload/62b19532ead6ff6467df9660.png)

トークンを作成したらコピーしておきましょう。

#### Visual Studio Marketplaceでpublisherを作成する

次にpublisherを作成します。[Visual Studio Marketplace](https://marketplace.visualstudio.com)でAzure DevOpsで使用したMicrosoftアカウントでログインして create publisherから追加します。vsceの[create-publisher]コマンドは使用不可になっているみたいなのでMarketplaceで作成しておきます。

#### 作成したpublisherでログインする

先ほど作成したpublisherでログインします。以下コマンド入力するとPersonal Access Tokensを求められるのでコピーしておいたトークンを使用します。

```
vsce login <publisher name>
```

#### 公開する

最後に以下コマンドで公開できます。公開するのは簡単ですね。

```
vsce publish
```

バージョンの更新は以下のように指定できます。

```
vsce publish minor  // minor versionを更新したい場合
vsce publish 2.0.1  // vversionを指定したい場合
```

#### READMEなどを整える

READMEに記載した内容がMarketplaceで公開した時のOverviewに表示されるので、簡単に特徴や要件や使い方の説明を記載しておくとよいと思います。とりあえず下手でも英語で記載しておくといいと思います。

Marketplace上に表示されるロゴもpackage.jsonで指定することができたりするので作成するといいと思います。

READEMEだけ更新する場合でもpublishしないといけないので、バージョンをあげる時にREADMEも合わせて更新するように注意したほうがいいです。

## まとめ

簡単な拡張機能は作って公開することは結構簡単なので、興味ある方は試してみてはいかがでしょうか。
あと、JavaScriptやTypeScriptを書く方はよかったら使ってみてください〜🙏

https://marketplace.visualstudio.com/items?itemName=N-Iwata.vscode-extention-js-console

ToDoとしては機能の追加やCI/CD環境の構築あたりを今後やっていこうと思っています。

最後まで読んでいただきありがとうございました🙇‍♂️

## 参考

公式ドキュメント
https://code.visualstudio.com/api

もともと使っていた拡張機能はこちら
https://marketplace.visualstudio.com/items?itemName=whtouche.vscode-js-console-utils