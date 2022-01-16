---
title: "お勧めの media query mixin：sass-mq"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "visual studio code", "npm"]
published: true
---

この記事では、私がいつも使用しているメディアクエリの`mixin`である[sass-mq](https://www.npmjs.com/package/@mcaskill/sass-mq)について紹介します。

## sass-mqの概要

`sass-mq`は、イギリスのGuardian社で開発された、メディアクエリの`mixin`になります。現在の主なメンテナーは、Shopify社のデザインシステムである[Polaris](https://polaris.shopify.com/)のUX開発マネージャーや[Design Tokens W3C Community Group](https://www.w3.org/community/design-tokens/)の創設者でもある[Kaelig](https://twitter.com/kaelig)です。

https://www.kaelig.fr/

`sass-mq`の`media feature`は、`min-width`,`max-width`,`orientation`に限定したシンプルなパッケージになります。

下のグラフは、2017年から2021年の5年間のダウンロード数になります（[npm trends](https://github.com/johnmpotter/npm-trends)）。数多くのデベロッパーがこのパッケージを使用している事がわかります。

![sass-mq weekly downloads](/images/my-favorite-sass-mq/npm-trends.png)


## sass-mqを使う理由
`sass-mq`には、次のような機能が備わっています。

1. `media-feature`のビューボート幅の単位を`px`から`em`に変換（`px2em` functionが実装済み）
2. `media-type`の指定が可能（`screen`, `print`, `all`）
3. `and`のロジックにも対応
4. バリデーション、エラーメッセージ機能
5. 開発時にアクティブなブレイクポイントをサイトの右上に表示できる機能

上記の機能を実装した自作の`mixin`を作成するには、とても労力のかかる作業になりますので、私は`sass-mq`を利用しています。`sass-mq`は、[多くの企業やデベロッパーが、プロジェクトで使用されています](https://github.com/sass-mq/sass-mq#who-uses-sass-mq)ので、実績もあります。

上記の機能の項目"5"は開発時にとても役に立ちます。実際の動作は[公式ドキュメント](https://github.com/sass-mq/sass-mq#seeing-the-currently-active-breakpoint)を参照ください。

## sass-mqのセッティング

`sass-mq`をインストールしたら、デフォルトの設定を上書きするための、`_mq-settings.scss`ファイルを作成します。

```scss: _mq-settings.scss
$breakpoints: (
    mobile:  360px,
    landscape: 540px,
    tablet:  700px,
    desktop: 1080px,
    wide:    1300px,
    full:    1920px
);

$media-type: 'all';

$show-breakpoints: (mobile, landscape, tablet, desktop, wide, full);

@forward './mq'  with (
  $breakpoints: $breakpoints,
  $media-type: $media-type,
  $show-breakpoints: $show-breakpoints,
);
```

ブレイクポイントの名前については、チーム内でコミュニケーションを取り、`sm`/`md`/`lg`/`xl`のようにしても良いかと思います。ブレイクポイントの値についても、デザインによって最適な値を設定・微調整する事になるかと思います。

私は、`abstracts`ディレクトリの`vendors`内で、`_mq-settings.scss`ファイルを管理しています。

```: SCSS Architecture（抜粋）
└── scss
    ├── abstracts
    │   ├── _index.scss
    │   ├── functions
    │   │   └── _index.scss
    │   ├── mixins
    │   │   ├── _focus.scss
    │   │   └── _index.scss
    │   ├── variables
    │   │   └── _index.scss
    │   └── vendors
    │       ├── _frontline.scss
    │       ├── _hover.scss
    │       ├── _index.scss
    │       ├── _mq-settings.scss
    │       └── _mq.scss
    ├── base
    │   ├── _index.scss
    ├── layout
    │   ├── _index.scss
    ├── object
    │   ├── _index.scss
    │   ├── components
    │   │   ├── _index.scss
    │   ├── project
    │   │   ├── _index.scss
    │   │   └── top
    │   │       ├── _index.scss
    │   └── utility
    │       ├── _index.scss
    └── style.scss
```

```scss: abstracts/_index.scss
// ============================================================================
// Abstracts
// ============================================================================

@forward 'functions';
@forward 'mixins';
@forward 'variables';
@forward 'vendors';
```

```scss: abstracts/vendors/_index.scss
// ============================================================================
// Vendors
// ============================================================================

@forward 'frontline';
@forward 'hover';
@forward 'mq-settings';
```

参考までに、SCSS Architectureは、このあたりの記事を参考に構築しています。
・[A Simple SCSS Architecture, and Best Practice Playbook](https://matthewelsom.com/blog/simple-scss-playbook.html)
・[2 SMARTEST WAYS TO STRUCTURE SASS](https://www.webdesignerdepot.com/2020/12/2-smartest-ways-to-structure-sass/)
・[Structuring your Sass Projects](https://itnext.io/structuring-your-sass-projects-c8d41fa55ed4)
・[How to structure your Sass codebase](https://remote.com/blog/how-to-structure-your-sass-project)


## 基本的な使い方
使い方はシンプルです。前のセクションで説明した、`abstracts`ディレクトリを`@use`で読み込んで、`mixin`を使用します。

```scss: layout/_footer.scss
@use '../abstracts/' as *;

.l-footer__list {
  @include mq($from: tablet) {
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
```

コンパイルされたCSSは次のようになります。

```css: compiled
@media (min-width: 43.75em) {
  .l-footer__list {
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
```

## ユーザースニペットの登録
`sass-mq`のスニペットはプラグイン等で用意されていませんので、私は`Visual Studio Code`の[User Snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_create-your-own-snippets)を活用して、3文字の`prefix`で呼び出せるようにしています。

```json: scss.json
  "sass-mq-fm": {
    "prefix": "mfm",
    "body": ["@include mq(\\$from: mobile) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-fl": {
    "prefix": "mfl",
    "body": ["@include mq(\\$from: landscape) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-ft": {
    "prefix": "mft",
    "body": ["@include mq(\\$from: tablet) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-fd": {
    "prefix": "mfd",
    "body": ["@include mq(\\$from: desktop) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-fw": {
    "prefix": "mfw",
    "body": ["@include mq(\\$from: wide) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-um": {
    "prefix": "mum",
    "body": ["@include mq(\\$until: mobile) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-ul": {
    "prefix": "mul",
    "body": ["@include mq(\\$until: landscape) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-ut": {
    "prefix": "mut",
    "body": ["@include mq(\\$until: tablet) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-ud": {
    "prefix": "mud",
    "body": ["@include mq(\\$until: desktop) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
  "sass-mq-uw": {
    "prefix": "muw",
    "body": ["@include mq(\\$until: wide) {", "\t$1", "}"],
    "description": "https://www.npmjs.com/package/@mcaskill/sass-mq"
  },
```

## 最後に
`sass-mq`および`User snippets`を利用する事で、コーディングの品質と効率の向上に繋がると思います。

そして、`sass-mq`のメンテナーでもある[Kaelig](https://twitter.com/kaelig)に改めて感謝いたします。