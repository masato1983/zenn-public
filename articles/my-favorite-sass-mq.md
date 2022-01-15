---
title: "お勧めの media query mixin：sass-mq"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "chrome", "プログラミング"]
published: false
---

この記事では、私がいつも使用しているメディアクエリの`mixin`である[sass-mq](https://www.npmjs.com/package/@mcaskill/sass-mq)について紹介します。

## About sass-mq

`sass-mq`は、イギリスのGuardian社で開発された、メディアクエリの`mixin`になります。`media feature`は、`min-width`,`max-width`,`orientation`に限定したシンプルなパッケージになります。

下のグラフは、2017年から2022年の5年間のダウンロード数になります（[npm trends](https://github.com/johnmpotter/npm-trends)）。数多くのデベロッパーがこのパッケージを使用している事がわかります。

![sass-mq weekly downloads](/images/my-favorite-sass-mq/npm-trends.png)


## Why use sass-mq?
`sass-mq`には、次のような機能が備わっています。

1. `media-feature`のビューボート幅の単位を`px`から`em`に変換（`px2em` functionが実装済み）
2. `media-type`の指定が可能（`screen`, `print`, `all`）
3. `and`のロジックにも対応
4. バリデーション、エラーメッセージ機能
5. 開発時にアクティブなブレイクポイントをサイトの右上に表示できる機能

上記の機能を実装した自作の`mixin`を作成するには、とても労力のかかる作業になります。[多くの企業やデベロッパーが、sass-mqをプロジェクトで使用している](https://github.com/sass-mq/sass-mq#who-uses-sass-mq)ので実績もあり信頼して使用する事ができます。

項目"5"の機能はとても役に立ちます。実際の動作は[公式ドキュメント](https://github.com/sass-mq/sass-mq#seeing-the-currently-active-breakpoint)を参照ください。

## Settings sass-mq

`sass-mq`をインストールしたら、デフォルト設定を上書きするための、`_mq-settings.scss`ファイルを作成します。

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

ブレイクポイントの名前については、チーム内でコミュニケーションを取り、`sm`/`md`/`lg`/`xl`のようにしても良いかと思います。ブレイクポイントの値についても、正解はなくデザインによって最適な値を設定する事になるかと思います。

`_mq-settings.scss`ファイルは、`abstracts`ディレクトリの`vendors`内で管理しています。

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

`sass-mq`を含めた`mixin`や`function`、`variables`を呼び出すときは、`@use`で`abstracts`ディクレトリをインポートします。 

参考までに、SCSS Architectureは、このあたりの記事を参考に構築しています。
・[A Simple SCSS Architecture, and Best Practice Playbook](https://matthewelsom.com/blog/simple-scss-playbook.html)
・[2 SMARTEST WAYS TO STRUCTURE SASS](https://www.webdesignerdepot.com/2020/12/2-smartest-ways-to-structure-sass/)
・[Structuring your Sass Projects](https://itnext.io/structuring-your-sass-projects-c8d41fa55ed4)
・[How to structure your Sass codebase](https://remote.com/blog/how-to-structure-your-sass-project)


## Basic usage
基本的な使い方は、シンプルです。

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

## 最後に



このパッケージをメインでメンテナンスされている、Shopify社のデザインシステムである[Polaris](https://polaris.shopify.com/)のUX開発マネージャーでもある[Kaelig](https://twitter.com/kaelig)に感謝いたします。