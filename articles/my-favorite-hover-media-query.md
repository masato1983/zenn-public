---
title: "お勧めのメディアクエリのmixin：hover-media-query"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "vscode", "npm"]
published: false
---

前回の記事では、メディアクエリのsass-mqに関する記事を書きましたが、今回は、も使用しているメディアクエリの`hover`の`mixin`である[hover-media-query](https://github.com/scriptex/hover-media-query)について紹介します。

## hover-media-queryの概要

このパッケージの作者は、[ブルガリアのバルナ](https://www.google.com/maps/place/%E3%83%96%E3%83%AB%E3%82%AC%E3%83%AA%E3%82%A2+%E3%83%90%E3%83%AB%E3%83%8A/@43.2047556,18.9780572,5z/data=!4m5!3m4!1s0x40a4538baaf3d7a1:0x5727941c71a58b7c!8m2!3d43.2140504!4d27.9147333)のデベロッパー[Atanas Atanasov](https://twitter.com/scriptexbg)です。

2021年4月に`ver1.0.0`が公開され、2022年1月現在は、`ver1.1.0`で、比較的新しいパッケージになります。

## :hover 擬似要素の問題点
`:hover`擬似要素の問題点は、スマホやタブレットなどのタッチスクリーン上で発生します。対象の要素をタップした際、タップ後もホバースタイルが適用され続けてしまい、デザイナーにとって意図しないインタラクションになるでしょう。

それを示すデモは、[Mezo Istvan](https://twitter.com/mezoistvan_)のこちらのブログで見ることができます。

https://medium.com/@mezoistvan/finally-a-css-only-solution-to-hover-on-touchscreens-c498af39c31c

この問題を解決するのが、[Media Queries Level 4 (25 December 2021)](https://drafts.csswg.org/mediaqueries/)のワーキングドラフトで定義されている、[Interactin Media Feature](https://drafts.csswg.org/mediaqueries/#mf-interaction)の3つの仕様になります。

1. Pointing Device Quality: the pointer feature（精度のクエリ）
2. Hover Capability: the hover feature（プライマリーインプットのホバー機能のクエリ）
3. All Available Interaction Capabilities: the any-pointer and any-hover features（すべてのポインティングデバイスとホバー機能に対するクエリ）

今回の問題の解決に最適なクエリは、"2"の`Hover Capability`になります。上記の`Mezo Istvan`のブログでも、Surfaceなどのタッチスクリーンを搭載したラップトップにおいて、タッチパッド/キーボードを取り外した際にタブレットモードになり、メディアクエリはそれを正しく処理すると書かれています。

> This means no hover effects on touchscreen only devices, and using them everywhere else. The special case here is laptops with touchscreens, but there we can expect that the mouse/touchpad is used most of the time. Even if there is a stuck hover effect, the user can easily use the mouse/touchpad to double-check the problem and dismiss it. Fortunately laptops with detachable touchscreens will go into a tablet mode when detached, which the media query correctly handles.

## 使い方
`hover-media-query`の

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

ホバーしたい要素で、`@include`すれば良いでしょう。具体例を示します。

```scss: input

.c-button {

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

```

## ターゲットデバイスに適用されるメディアクエリの確認
ターゲットのデバイスがどのようにUAが判断したのかを確認する[テストサイト](https://hover-pointer-media-query.glitch.me/)があります。テスト結果は以下のように表示されます。

![media-query-tester](/images/my-favorite-hover-media-query/media-query-tester.png)

手元にある、Mac/iPad/iPhone/androidでテストした結果が以下になります（iPad/iPhone/androidは、タッチスクリーンでテストしています）

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
本記事を書くにあたり、以下の記事を参考にさせて頂きました。

- [Finally, a CSS only solution to :hover on touchscreens](https://medium.com/@mezoistvan/finally-a-css-only-solution-to-hover-on-touchscreens-c498af39c31c)
- [Interaction Media Features and Their Potential (for Incorrect Assumptions)](https://css-tricks.com/interaction-media-features-and-their-potential-for-incorrect-assumptions/)
- [Touch Devices Should Not Be Judged By Their Size](https://css-tricks.com/touch-devices-not-judged-size/)
- [MDN Web Docs @media hover](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/hover)
- [MDN Web Docs @media any-hover](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/any-hover)
- [MDN Web Docs @media any-pointer](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/any-pointer)


## 最後に
ユーザーに混乱を与えるインタラクションをしないよう、適切にメディアクエリを適用する必要があります。`hover-media-query`を活用してみてください。

そして、`hover-media-query`の`mixin`を用意してくださった、[Atanas](https://twitter.com/scriptexbg)さんに感謝いたします。