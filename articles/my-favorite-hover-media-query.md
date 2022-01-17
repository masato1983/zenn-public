---
title: "お勧めのメディアクエリのmixin：hover-media-query"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "vscode", "npm"]
published: true
---

この記事では、私がいつも使用しているメディアクエリの`hover`の`mixin`である
[hover-media-query](https://github.com/scriptex/hover-media-query)について紹介します。

## hover-media-queryの概要

このパッケージの作者は、[ブルガリアのバルナ](https://www.google.com/maps/place/%E3%83%96%E3%83%AB%E3%82%AC%E3%83%AA%E3%82%A2+%E3%83%90%E3%83%AB%E3%83%8A/@43.2047556,18.9780572,5z/data=!4m5!3m4!1s0x40a4538baaf3d7a1:0x5727941c71a58b7c!8m2!3d43.2140504!4d27.9147333)のデベロッパー[Atanas Atanasov](https://twitter.com/scriptexbg)になります。

2021年4月に`ver1.0.0`が公開され、2022年1月現在は`ver1.1.0`となっており、比較的新しいパッケージになります。

## :hover 擬似クラスの問題点
擬似クラスの`:hover`のみだけで要素をスタイリングした場合、プライマリーインプットがマウスなどのホバー機能を持ったデスクトップの場合は問題ありませんが、スマホやタブレットなどのタッチスクリーンデバイスでは問題が発生します。下の[Mezo Istvan](https://twitter.com/mezoistvan_)のブログにも紹介されているように、タッチスクリーンデバイスで要素をタップした際、タップ後もホバースタイルが適用され続けてしまいます。

https://medium.com/@mezoistvan/finally-a-css-only-solution-to-hover-on-touchscreens-c498af39c31c

MDNでも同様の内容が記載されており、スマホやタブレットなどのタッチスクリーンデバイスで、ホバースタイルが当てられた要素をタップすると、フラッシュ（ちらつき）の原因になったります。

https://developer.mozilla.org/en-US/docs/Web/CSS/:hover

この問題を解決するのが、[Media Queries Level 4 (25 December 2021)](https://drafts.csswg.org/mediaqueries/)のワーキングドラフトで定義されている、[Interactin Media Feature](https://drafts.csswg.org/mediaqueries/#mf-interaction)になります。

`Interaction Media Feature`には、3つのクエリがあります。

1. `Pointing Device Quality`: the pointer feature（精度のクエリ）
2. `Hover Capability`: the hover feature（プライマリーインプットのホバー機能のクエリ）
3. `All Available Interaction Capabilities`: the any-pointer and any-hover features（すべてのポインティングデバイスとホバー機能に対するクエリ）

今回の`:hover`擬似クラスの問題を回避する最適なクエリは、`Hover Capability`になります。上記の`Mezo Istvan`のブログにも紹介されていますが、Surfaceなどのタッチスクリーンを搭載したラップトップにおいて、タッチパッド/キーボードを取り外すとタブレットモードになり、メディアクエリはそれを正しく処理すると書かれています。

## 使い方
これまで説明してきた`Hover Capability`を楽に導入する事ができるパッケージが、[hover-media-query](https://github.com/scriptex/hover-media-query)になります。

https://github.com/scriptex/hover-media-query

ソースコードはとてもシンプルです。`hover CSS Media Feature`をサポートしていない、`IE10`/`IE11`/`Firefox before 64`も対象としています。

```scss: hover.scss
@mixin hover {
	@media (hover: none) {
		-webkit-tap-highlight-color: rgba(0, 0, 0, 0);

		&:active {
			@content;
		}
	}

	@media (-ms-high-contrast: none), (-ms-high-contrast: active), (-moz-touch-enabled: 0), (hover: hover) {
		&:hover {
			@content;
		}
	}
}
```

あとは、ホバーしたい要素に`@include`で`mixin`を呼び出して`hover`スタイルを適用します。下のコードは`button`コンポーネントに`hover`のメディアクエリを使用して、`:hover`擬似クラスを適用した例になります。（`hover-medea-query`のコードは、`abstracts`ディレクトリ内で管理しています）

```scss: input
@use '../../abstracts/' as *;

/* base style */
.c-button {
  display: inline-flex; 
  align-items: center;
  justify-content: center;
  width: 100%;
  margin: 0 auto;
  border-radius: 100vh; // rounded corners

  // active style
  &:active {
    transform: translateY(fs-rem(1px));
  }

  // diabled style
  &[disabled] {
    opacity: 0.65;
    pointer-events: none;
  }
}

/* primary modifier */
.c-button--primary {
  background-color: var(--color-neutral-lightest);
  border: fs-rem(2px) solid var(--color-primary-mid);
  transition: background-color 0.3s var(--ease-in-out-cubic);

  // hover style
  @include hover {
    background-color: var(--color-primary-mid-alpha-high);
  }
```

コンパイルした結果がこちらになります。

```css: output
.c-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  margin: 0 auto;
  border-radius: 100vh;
  border-radius: calc(var(--vh, 1vh) * 100);
}

.c-button:active {
  transform: translateY(0.0625rem);
}

.c-button[disabled] {
  opacity: 0.65;
  pointer-events: none;
}

.c-button--primary {
  background-color: var(--color-neutral-lightest);
  border: 0.125rem solid var(--color-primary-mid);
  transition: background-color 0.3s var(--ease-in-out-cubic);
}

@media (hover: none) {
  .c-button--primary {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  }

  .c-button--primary:active {
    background-color: var(--color-primary-mid-alpha-high);
  }
}

@media (-ms-high-contrast: none), (-ms-high-contrast: active), (-moz-touch-enabled: 0), (hover: hover) {
  .c-button--primary:hover {
    background-color: var(--color-primary-mid-alpha-high);
  }
}
```

`hover-media-query`の`mixin`を使用することで、記述量を減らすことができるので助かっています。

## ターゲットデバイスに適用されるメディアクエリの確認
ターゲットのデバイスがどのメディアクエリを適用するのかを[テストするサイト](https://hover-pointer-media-query.glitch.me/)があります。下の画像は、私が使用しているMac Miniのテスト結果になります。主要な入力メカニズムがポインティングデバイスであり、ホバー機能を持っている事がわかります。

![media-query-tester](/images/my-favorite-hover-media-query/media-query-tester.png)

手元にある、`Mac Mini`/`iPad`/`iPhone`/`android`でテストしてみた結果が以下になります（`iPad`/`iPhone`/`android`はタッチスクリーンでテストしています）。このようにしてターゲットデバイスに適用される、メディアクエリを確認する事もできます。

| - | Mac  | iPad | iPhone | android |
| ---- | ---- | ---- | --- | --- |
| @media(hover: none) | False | True | True |  True | 
| @media(hover: hover) | True | False | False | False |
| @media(any-hover: none) | False | True | True |  True | 
| @media(any-hover: hover) | True | False | False | False |
| @media(pointer: none) | False | False | False | False |
| @media(pointer: fine) | True | False | False | False |
| @media(pointer: coarse) | False | True | True |  True | 
| @media(any-pointer: none) | False | False | False | False |
| @media(any-pointer: fine) | True | False | False | False |
| @media(any-pointer: coarse) | False | True | True |  True | 


## 参考記事
本記事を書くにあたり、以下の記事を参考にしました。

- [Finally, a CSS only solution to :hover on touchscreens](https://medium.com/@mezoistvan/finally-a-css-only-solution-to-hover-on-touchscreens-c498af39c31c)
- [Interaction Media Features and Their Potential (for Incorrect Assumptions)](https://css-tricks.com/interaction-media-features-and-their-potential-for-incorrect-assumptions/)
- [Touch Devices Should Not Be Judged By Their Size](https://css-tricks.com/touch-devices-not-judged-size/)
- [MDN Web Docs @media hover](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/hover)
- [MDN Web Docs @media any-hover](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/any-hover)
- [MDN Web Docs @media any-pointer](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/any-pointer)


## 最後に
ユーザーに混乱を与えるインタラクションをしないよう、適切に`hover`のメディアクエリを適用する必要があります。[hover-media-query](https://github.com/scriptex/hover-media-query)を活用することにより、数行のコードで`:hover`擬似クラスの問題点を回避することができます。皆さんも試してみてください。

そして、`hover-media-query`の`mixin`を用意してくださった、[Atanas](https://twitter.com/scriptexbg)さんに感謝いたします。