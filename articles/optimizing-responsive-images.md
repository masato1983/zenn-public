---
title: "静的サイトにおけるレスポンシブ画像の最適化および検証ツール：Resp Image Lint"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "chrome", "プログラミング"]
published: true
---


この記事では、レスポンシブ画像の最適化および検証ツールとして活用している、[Resp Image Lint](https://github.com/ausi/RespImageLint)と呼ばれるツール（ブックマークレット）を紹介したいと思います。

## Responsive画像の最適化
`Resp Image Lint`についてお話しする前に、レスポンシブ画像のマークアップについて触れておきます。レスポンシブ画像を正しくマークアップするには、次の4つのキーワードについて正しく理解しておく必要があります。

1. 画像サイズの変更（sizes）
2. 解像度の変更（dpi）
3. マイムタイプの変更（mime）
4. アートディレクション（art）

上記4つのユースケースの全ての組み合わせを網羅した記事が[Dev.Opera](https://dev.opera.com/articles/responsive-images/)で紹介されており、いつも参考にしています。更に詳しく知りたい方は、[Cloudfour](https://cloudfour.com/)の[Responsive Images 101 Serise](https://cloudfour.com/thinks/responsive-images-101-definitions/)や、[Responsive Images the Simple Way](https://cloudfour.com/thinks/responsive-images-the-simple-way/)の記事も参照される事をお勧めします。


## マークアップの問題点
上記の記事を参考にマークアップした後、正しくマークアップできているか、どのように品質を担保すれば良いのでしょうか？

特に、`sizes`属性の値の単位はビューポートユニットである`vw`を使用することが多いので、画像が固定幅の`px`や画面幅いっぱいの`100vw`の画像であれば問題はありませんが、コンテナ内に画像が配置されている場合などは、正しく導出するのに時間がかかり、果たして本当に正しい値なのか心配になる事があります。

そんな不安を解消してくれるツールが、次のセクションで紹介する[Resp Image Lint](https://github.com/ausi/RespImageLint)になります。


## Resp Image Lintの概要
Resp Image Lintの作者は、オーストリアのザルツブルグにあるウェブ制作やフレームワークを提供している会社 "[MADE/YOUR/DAY](https://myd.at/)" のウェブデベロッパーの[Martin Auswöger](https://twitter.com/ausi)です。

`Resp Image Lint`は、画像に関するマークアップを解析し、18項目の厳密なチェックを行ってくれます。[ドキュメント](https://ausi.github.io/respimagelint/docs.html)には、`Correct`/`Incorrect`の具体例も掲載されていますので、一度軽く目を通しおくと良いと思います。

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


## Resp Image Lintの注目の機能
2017年4月に注目すべき機能が追加されました。レスポンシブ画像の`<source>`や`<img>`タグの`sizes`属性の値が正しくない、もしくは欠落している場合、コピーペースト可能な`sizes`属性を自動的に生成して、デベロッパーに提案してくれる機能になります。

この機能が追加された時の、作者のTweetが下記になります。


@[tweet](https://twitter.com/ausi/status/852637758359298048)


:::message
ただ1つ留意する点があるとすれば、[メディアクエリの解析は20px単位で実行されます](https://github.com/ausi/respimagelint/issues/25#issuecomment-294025130)。しかし、これまで使用してきた経験から、大勢に影響はない小さなレベルの問題と考えています。
:::


## Resp Image Lintの使い方
使い方はとても簡単で、次の4ステップになります。

1. [こちらの公式ページ](https://ausi.github.io/respimagelint/)に移動して、`Lint Images`のボタンをブックマークバーにドラッグするか、Chromeであればショートカットの`⌘D`でブックマーク登録します。
2. 検証したいサイトのページに移動して、先ほどブックマークした`Resp Image Lint`のアイコンをクリックします。
3. ツールが起動され、さまざまなビューポートサイズとピクセル密度でページが自動的にスキャンされます。
4. レポートが作成され、問題があった箇所を修正して完了です（`sizes`属性に関する問題があった場合のみ、`Resp Image Lint`は最適な値を提案してくれます）。

このような機能は些細かもしれませんが、本当に助かります。


## 紹介事例
この便利なツールは、[CSS-Tricks](https://twitter.com/css/status/978390008485724160)や[Smashing Magazine](https://twitter.com/smashingmag/status/732537169902899200)、そしてデベロッパーである[Sia Karamalegos](https://twitter.com/TheGreenGreek)のブログの中でも紹介されています。

https://sia.codes/posts/eleventy-and-cloudinary-images/#use-srcset-and-sizes-so-the-browser-can-pick-the-best-image

素晴らしいメディアやデベロッパー達が紹介しているので、信頼もできるのではないでしょうか。

## 最後に
静的サイトのレスポンシブ画像の最適化に、[Resp Image Lint](https://github.com/ausi/respimagelint)を使用する事で、効率と品質を向上させる事ができると思います。

このような素晴らしいツールを作成してくれた[Martin](https://twitter.com/ausi)には感謝いたします。

皆さんも是非試してみてください。

