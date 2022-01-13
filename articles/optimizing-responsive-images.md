---
title: "静的サイトにおけるレスポンシブ画像の最適化および検証ツール：Resp Image Lint"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "chrome", "プログラミング"]
published: false
---


この記事では、レスポンシブ画像の最適化および検証ツールとして活用している、[Resp Image Lint](https://github.com/ausi/RespImageLint)と呼ばれるツール（ブックマークレット）を紹介したいと思います。

## Responsive画像の最適化
Resp Image Lintについてお話しする前に、レスポンシブ画像のマークアップについて触れておきます。レスポンシブ画像を正しくマークアップするには、次の4つのキーワードについて正しく理解しておく必要があります。

1. sizes：画像サイズの変更
2. dpi：解像度の変更
3. mime：マイムタイプの変更
4. art：アートディレクション

上記4つのユースケースの全ての組み合わせを網羅した記事が[Dev.Opera](https://dev.opera.com/articles/responsive-images/)で紹介されており、いつも参考にしています。更に詳しく知りたい方は、[Cloudfour](https://cloudfour.com/)の[Responsive Images 101 Serise](https://cloudfour.com/thinks/responsive-images-101-definitions/)や、[Responsive Images the Simple Way](https://cloudfour.com/thinks/responsive-images-the-simple-way/)の記事を参照される事をお勧めします。

## マークアップの問題点
上記の記事を参考にマークアップした後、正しくマークアップできているか、どのように品質を担保すれば良いのでしょうか？

特に、`sizes`属性の値の単位はビューポートユニットである`vw`を使用することが多いので、画像が固定幅の`px`や画面幅の`100vw`の画像であれば問題はありませんが、コンテナ内に画像が配置されている場合などは、正しく導出するのに時間がかかり、果たして本当に正しい値なのか心配になる事があります。

そんな不安を解消してくれるツールが、次のセクションで紹介する[Resp Image Lint](https://github.com/ausi/RespImageLint)になります。

## Resp Image Lintの概要
Resp Image Lintの作者は、オーストリアのザルツブルグにあるウェブ制作やフレームワークを提供している[MADE/YOUR/DAY](https://myd.at/)に所属しているウェブデベロッパーの[Martin Auswöger](https://twitter.com/ausi)です。

https://twitter.com/ausi/status/852637758359298048

Resp Image Lintは、画像に関するマークアップを解析し、18項目の厳密なチェックを行ってくれます。[ドキュメント](https://ausi.github.io/respimagelint/docs.html)には、具体例も掲載されていますので、一度目を通しおくと良いかと思います。

1. Descriptors must be unique
2. Malformed descriptor
3. X and W descriptors must not be mixed in one srcset attribute
4. Sizes attribute must be set if W descriptors are used
5. W descriptor doesn’t match the image size
6. X descriptor doesn’t match the image size
7. X descriptor must not be used if sizes attribute is set
8. Missing `src` attribute
9. Images in `srcset` attribute must not be different
10. Images in `srcset` attribute must not have different aspect ratios
11. A fitting image source should be available for all screen sizes
12. Images in different `<source>` elements shouldn’t be the same
13. The `sizes` attribute has to match the width of the image
14. Multiple `<img>` elements are not allowed
15. Only `<source>` and `<img>` tags are allowed inside of `<picture>`
16. The `<img>` element must not be omitted inside of `<picture>`
17. The `src` attribute has no effect on a `<source>` element
18. The `<source>` element must not appear after an `<img>` element


## Resp Image Lintの使い方
使い方はとても簡単です。次の4ステップです。

1. [こちらの公式ページ](https://ausi.github.io/respimagelint/)に移動して、`Lint Images`のボタンをブックマークバーにドラッグするか、Chromeであればショートカットの`⌘D`でブックマーク登録してください。
2. 検証したいサイトのページに移動して、先ほどブックマークした`Resp Image Lint`のアイコンをクリックしてください。
3. ツールが起動され、さまざまなビューポートサイズとピクセル密度でページを自動的にスキャンします。
4. レポートが作成され、問題があれば修正方法を提案してくれるので、その通りにコードを修正して完了です。

![](/images/optimizing-responsive-images/2022-01-13-01.png)

:::message
Resp Image Lintを使用する最大のメリットは、上記の18項目をチェックするだけではなく、最適な値を提案してくれる点です。
:::

## 紹介事例
この便利なツールは、[CSS-Tricks](https://twitter.com/css/status/978390008485724160)や[Smashing Magazine](https://twitter.com/smashingmag/status/732537169902899200)のTwitterやデベロッパーの[Sia Karamalegos](https://twitter.com/TheGreenGreek)も以下のブログで紹介しています。

https://sia.codes/posts/eleventy-and-cloudinary-images/#use-srcset-and-sizes-so-the-browser-can-pick-the-best-image

## 最後に
いかがだったでしょうか？静的サイトのレスポンシブ画像の最適化に、[Resp Image Lint](https://github.com/ausi/respimagelint)を使用する事で、効率と品質を向上させる事ができると思います。皆さんも是非試してみてください。

