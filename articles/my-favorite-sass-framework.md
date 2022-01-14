---
title: "お気に入りのSassフレームワーク：Threespot's Sass framework（Frontline Sass）"
emoji: "👏"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["html", "css", "chrome", "プログラミング"]
published: true
---

この記事では、私のお気に入りのSassフレームワークである、[Frontline Sass](https://github.com/Threespot/frontline-sass-snippets)を紹介します。日本では、ほぼ認知されていないのが不思議なくらい素晴らしいフレームワークだと思っています。

## About Threespot
Frontline Sassは、[Threespot](https://www.threespot.com)社が提供しているフリーのCSSフレームワークです。Threespot社は1999年に設立されたワシントンD.C.を拠点とするコミュニケーションおよびデザインのエージェンシーになります。2015年には、"Certified B Corporation®"の[認証](https://www.bcorporation.net/en-us/find-a-b-corp/company/threespot/)を受けており、環境や社会に配慮した事業活動を行なっている事が[制作実績](https://www.threespot.com/our-work)からも見てとることができます。

## Frontline Sassの構成
Frontline Sassのドキュメントは[こちら](https://threespot.github.io/frontline-sass/documentation/)で、構成は以下のようになっています。

- main
    - functions
    - mixins
    - variables
- math
    - variables
    - functions
- sass utils
    - functions
    - variables
    - mixins
- selectors
    - mixins

## Frontline Sassでお勧めのfunctionとmixin
私が頻繁に使用するfunctionと関数を3つ紹介します。

### 1. fs-rem (function)
`fs-rem`は、`px`を`rem`に変換する`function`になります。はじめは自作の関数を定義して使用していましたが、バリデーションが盛り込まれたロバスト性の高い`fs-rem`に切り替えました。Threespot社で使用されている実績も判断材料のひとつになります。

`fs-rem`の[ソースコード](https://github.com/Threespot/frontline-sass/blob/master/src/functions/_rem.scss)です。

```scss:source code
/// Convert px to rem
/// @group Main
/// @param {Number | Map} $values - Value values to convert
/// @param {Number} $context [$fs-base-font-size] - Base font size
/// @return {*} - Converted value or list of values
/// @require {variable} $fs-base-font-size - Base font size
@function fs-rem($values) {

  // Placeholder list for converted values
  $output: ();

  @each $val in $values {

    // Check if pixel value
    @if type-of($val) == 'number' and unit($val) == 'px' {
      // Convert to rems
      $val: ($val / $fs-base-font-size) * 1rem;
    }

    // Do nothing to all other values
    @else if $fs-debug-mode and $val != 0 {
      @warn '⚠️  Can’t convert non-pixel value to rems: #{$val}';
    }

    // Append value to output list
    $output: append($output, $val);
  }

  // Return converted value(s)
  @return $output;
}
```

実際の使用例を示します。

```scss: usage examples (input)
.c-heading__level2 {
  font-size: fs-rem(32px);
}
```

```scss: usage examples (output) 
.c-heading__level2 {
  font-size: 2rem;
}
```


### 2. fs-scale (function)
2つ目は、`fs-scale`のfunctionです。主にfluid typographyを目的として使用していますが、テキスト以外のマージンやパディングにも適用できます。ie11などの`min()`,`max()`,`clamp()`をサポートしていない場合に効果を発揮します。

`fs-scale`の[ソースコード](https://github.com/Threespot/frontline-sass/blob/master/src/functions/_scale.scss)です。

```scss: source code
/// Scale a value relative to the viewport width using `calc()` and viewport units
/// @group Main
/// @param {Number} $start - Starting value
/// @param {Number} $end - Ending value
/// @param {Number} $min-width - Starting viewport width
/// @param {Number} $max-width - Ending viewport width
/// @return {String} - Custom `calc()` formula
/// @ignore Demo https://www.sassmeister.com/gist/9f87f8c19e91c811831d84fc8484ed24
/// @link http://www.sassmeister.com/gist/7f22e44ace49b5124eec
/// @link http://madebymike.com.au/writing/precise-control-responsive-typography/
/// @link https://zellwk.com/blog/viewport-based-typography/
/// @link http://codepen.io/indrekpaas/pen/VarLaJ?editors=1100
/// @link http://codepen.io/maranomynet/pen/ozNpXV?editors=1100
/// @link https://www.smashingmagazine.com/2016/05/fluid-typography/
/// @example scss
///   p {
///     font-size: fs-scale(20px, 30px, 320px, 400px);
///   }
@function fs-scale($start, $end, $min-width, $max-width, $units: 'rem') {
  @if $start > 0 and $end > 0 and unit($start) != unit($end) {
    @error '🔴  fs-scale() units don’t match';
  }

  $slope: ($end - $start) / ($max-width - $min-width);

  $intercept: $end - ($slope * $max-width);

  @if (fs-strip-unit($intercept) == 0) {
    @return $slope * 100vw;
  }
  @else {
    @if $units == 'em' {
      @return calc(#{$slope * 100vw} + #{fs-em($intercept)});
    }
    @else {
      @return calc(#{$slope * 100vw} + #{fs-rem($intercept)});
    }
  }
}
```

実際の使用例を示します。

1行記述するだけで良いのでとても助かりますし、メディアクエリの単位が、`em`なのもお気に入りの理由のひとつになります。


```scss: usage examples (input)
.c-heading__level2 {
  @include fs-scale(font-size, (375px: 48px, 1366px: 92px)); 
}
```

```scss: usage examples (output)
.c-heading__levle2 {
  @media all and (min-width: 23.4375em) {
    h1 {
      font-size: calc(4.4399596367vw + 1.9593844601rem);
    }
  }

  @media all and (min-width: 85.375em) {
    h1 {
      font-size: 5.75rem;
    }
  }
}
```


### 3. fs-aspect-ratio、fs-absolute-fill (mixin)
3つ目は、`fs-aspect-ratio`と`fs-absolute-fill`の`mixin`になります。画像のアスペクト比を設定する際に使用する、パディングトップ（パディングボトム）と呼ばれるハックになります。

`fs-aspect-ratio`の[ソースコード](https://github.com/Threespot/frontline-sass/blob/master/src/mixins/_aspect-ratio.scss)になります。

```scss: source (fs-aspect-ratio)
/// Maintain aspect ratio
/// @group Main
/// @param {Number} $ratio - Aspect ratio (width / height)
///
/// @example scss - 16:9 aspect ratio
///   .foo {
///     @include fs-aspect-ratio(16/9);
///   }
///
@mixin fs-aspect-ratio($ratio) {
  display: block;
  overflow: hidden;
  position: relative;

  &:before {
    content: '';
    display: block;
    padding-top: percentage(1 / $ratio);
  }
}
```

`fs-absolute-fill`の[ソースコード](https://github.com/Threespot/frontline-sass/blob/master/src/mixins/_absolute-fill.scss)になります。

```scss: source (fs-absolute-fill)
/// Absolutely position an element and fill available space
/// @group Main
@mixin fs-absolute-fill {
  height: 100%;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
}
```

<br>

実際の使用例を示します。`fs-aspect-ratio`と`fs-absolute-fill`を使用する事で、コードの記述量が減ります。また`fs-aspect-ratio`と`fs-absolute-fill`という名前から機能を推測できるので、可読性も向上しているのではないかと思います。

```html: usage examples (input)
<div class="p-top-awards__media">
    <img class="p-top-awards__image" src="/assets/img/top/top-award-01-1040x600-cd638981.jpg" alt="" />
</div>
```

```scss: usage examples (input)
/* media layout */
.p-top-awards__media {
  @include fs-aspect-ratio(div(324, 187));
}

/* image layout */
.p-top-awards__image {
  @include fs-absolute-fill;
}
```

```scss: usage examples (output)
/* media layout */
.p-top-awards__media {
  position: relative;
  display: block;
  overflow: hidden;
}

.p-top-awards__media::before {
  display: block;
  padding-top: 57.7160493827%;
  content: '';
}

/* image layout */
.p-top-awards__image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
```

## VSCodeのスニペット
Frontline Sassには、AtomとVScodeのスニペットプラグインが用意されていますので、補完機能で効率よくコードを書くことができます。

https://marketplace.visualstudio.com/items?itemName=Threespot.frontline-sass-snippets


## 最後に
Frontline Sassには、今回紹介した以外のコーディングの助けとなる`functions`・`mixins`・`variables`が数多く揃っています。日本ではほとんど認知されていないので、もっと広まって欲しいと思います。

このような素晴らしいフレームワークを提供してくださる、[Threespot](https://www.threespot.com/)社に感謝いたします。
