---
title: "Progressive Enhancementもしくは私達が目指すべき姿について"
emoji: "😽"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["フロントエンド"]
published: false
---

昨今、Progressive Enhancementという言葉を耳にする機会が多くなったと感じています。
個人的に興味のある考え方であったので、少し調べて考えをまとめてみようと思います。

## Progressive Enhancementについて

MDNでは下記のように説明されています。

> プログレッシブエンハンスメント (Progressive enhancement) とは、可能な限り多くのユーザーに不可欠なコンテンツと機能のベースラインを提供することを中心とした設計哲学であり、必要なすべてのコードを実行できる最新のブラウザーのユーザーに限り、最高の体験を提供します。

https://developer.mozilla.org/ja/docs/Glossary/Progressive_Enhancement#:~:text=%E3%83%97%E3%83%AD%E3%82%B0%E3%83%AC%E3%83%83%E3%82%B7%E3%83%96%E3%82%A8%E3%83%B3%E3%83%8F%E3%83%B3%E3%82%B9%E3%83%A1%E3%83%B3%E3%83%88%20(Progressive%20enhancement)%20%E3%81%A8%E3%81%AF%E3%80%81%E5%8F%AF%E8%83%BD%E3%81%AA%E9%99%90%E3%82%8A%E5%A4%9A%E3%81%8F%E3%81%AE%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E3%81%AB%E4%B8%8D%E5%8F%AF%E6%AC%A0%E3%81%AA%E3%82%B3%E3%83%B3%E3%83%86%E3%83%B3%E3%83%84%E3%81%A8%E6%A9%9F%E8%83%BD%E3%81%AE%E3%83%99%E3%83%BC%E3%82%B9%E3%83%A9%E3%82%A4%E3%83%B3%E3%82%92%E6%8F%90%E4%BE%9B%E3%81%99%E3%82%8B%E3%81%93%E3%81%A8%E3%82%92%E4%B8%AD%E5%BF%83%E3%81%A8%E3%81%97%E3%81%9F%E8%A8%AD%E8%A8%88%E5%93%B2%E5%AD%A6%E3%81%A7%E3%81%82%E3%82%8A%E3%80%81%E5%BF%85%E8%A6%81%E3%81%AA%E3%81%99%E3%81%B9%E3%81%A6%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%A7%E3%81%8D%E3%82%8B%E6%9C%80%E6%96%B0%E3%81%AE%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E3%83%BC%E3%81%AE%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E3%81%AB%E9%99%90%E3%82%8A%E3%80%81%E6%9C%80%E9%AB%98%E3%81%AE%E4%BD%93%E9%A8%93%E3%82%92%E6%8F%90%E4%BE%9B%E3%81%97%E3%81%BE%E3%81%99%E3%80%82

### Graceful Degradationとの違いについて

Progressive Enhancementが基礎を作ってから積み上げていくようなイメージに対して、Graceful Degradationはとりあえず作ってから最低限のラインを補修するようなイメージでしょうか。
この２つは多くのユーザーに最低限必要な機能を届けるという点で非常に似ていますがアプローチと考え方が異なります。

https://developer.mozilla.org/ja/docs/Glossary/Graceful_degradation

### JSが無効な環境は重要な要素ではあるが全てではない

Progressive Enhancementを語る場合、フロントエンドにおいてはよくJSが無効化されている環境向けたHTMLやCSSの活用が挙げられることも多いと思います。
[Progressive Enhancement reading list 2021 - QuirksBlog](https://www.quirksmode.org/blog/archives/2021/02/progressive_enh_1.html#:~:text=I%20find%20any.-,Layer%203%3A%20JavaScript,-Then%20we%20come)のLayer 3: JavaScriptでも触れられている通り、この考え方は重要な要素ではありますがProgressive Enhancementの全てではないと思っています。

最初に記載したように「可能な限り多くのユーザーに不可欠なコンテンツと機能のベースラインを提供する」という観点では、確かにHTMLとCSSのみで基本的な機能を実装し発展的なUXをJavaScriptで実現するという手法は実に合理的だと思っています。

## 可能な限り多くのユーザーとは

しかし「可能な限り多くのユーザー」という定義であるならばProgressive Enhancementはまずはじめに古いブラウザのユーザーのことを考えるべきではないかという考えに至ります。

Progressive Enhancementの話題になるとよく引き合いに出される最新のHTMLやCSSの仕様について、当たり前ですがこれらは古いブラウザではサポートされていないことが多いです。
特にSafari 14.xなどでは現在〇〇%のユーザーがいますがCSSの`:has()`やHTMLの`popover`/`popoverTarget`などの属性値はサポートされていません。

<!-- TODO: 後でSafari 14.xのシェアとHTML/CSSのサポート状況を調べておく -->

HTMLやCSSのポリフィルはJavaScriptのポリフィルに比べて制約が多く、FlexGapやPopoverのポリフィルではREADMEに使用する際の注意事項が記載されています。

https://github.com/oddbird/popover-polyfill?tab=readme-ov-file#caveats

https://github.com/gavinmcfarland/flex-gap-polyfill?tab=readme-ov-file#known-limitations

エンジニアとしては最新ブラウザのみをサポートするという風にしたくなりますが、Progressive Enhancementが「可能な限り多くのユーザーに不可欠なコンテンツと機能のベースラインを提供する」ことを目的とするならば、まずは古いブラウザへの対応を考える必要があると考えます。

<!-- ポリフィルなどのパッケージは追加は簡単にできるけど削除するのが難しくポリフィルが肥大化してしまう問題について触れる -->

## 我々は何をサポートするべきなのか

理想論を書いてみましたが、すべてをサポートするというのは現実的ではありません。
サポート範囲を単純にブラウザバージョンで区切ることも可能ですが、JS/CSSの有効・無効やアクセシビリティ観点など、考慮することは多岐に渡りプロダクトの性質によって求められるサポート範囲は変わってくると思います。

ソフトウェアを作るためにはどのような環境をサポートするのか、しないのかという話は簡単に決められませんが非常に重要な項目だと考えています。

### 推奨環境と必須環境という考え方

急に話が変わりますが、PCゲームの世界では「動作推奨環境」と「動作必須環境」という考え方があります。  
これは家庭用ゲーム機のようにハードウェアが統一されていないPCゲームの世界において、快適に製品を遊べるかという重要な指標になっています。

動作推奨環境：ゲームが快適にプレイできるハードウェアスペックの基準
動作必須環境：ゲームを実行するのに最低限必要なハードウェアスペックの基準

![THE FINALSというゲームのシステム要件を表示している画面のスクリーンショット](/images/articles/progressive-enhancement/system-requirement.png)
*動作必須環境と動作推奨環境を比べてみるとCPUやGPUのスペックが高くなっているのが分かる*

### 品質の保証基準も範囲で考える

このように、Progressive EnhancementやGraceful Degradationの考え方を取り入れるのであれば要求環境も「最低」と「推奨」を分けるべきではないかと思いました。
あくまで例ですが、下記のように達成するべき基準を対応環境ごとに分けて定義することによってこれらの考え方をプロダクトの品質管理に反映できるのではないか、と考えています。

#### 推奨環境での動作

プロダクトのUX指標としての目標
最新バージョンのブラウザのみをサポートする。

- パフォーマンス指標
- デザインやインタラクションの再現性
- （高度な）アクセシビリティ対応

#### 必須環境での動作

プロダクトを基本的に利用するための目標
FirefoxとChromeは３バージョン前までSafariは14.0までをサポートする。

- プロダクトの利用が継続困難になるバグが無いか
- （基本的な）アクセシビリティ対応

## 私達ができること

私達ができること（もしくは目指すべき目標）としては

- 最低限アプリケーションが動作する環境と快適に動く環境を分けて考える。
- それらをそれぞれ定義した上でユーザーと約束をする。
- 基準に沿ってリリースが行えるように体制を整える。
- 定期的に動作環境を見直す。

これらのことができるようになるとProgressive EnhancementやGraceful Degradationを意識して開発ができるようになるのではないかという考えに至りました。

## 最後に

Progressive Enhancementについて調べてみたり、考えを巡らせてみたりした結果として、着地点があまり関係のないプロダクトの仕様の話になってしまいました。
内容も現在のプロダクト開発からすると、複数の動作環境を定義して品質保証をするというのはかなり理想論的な話だとも思っています。
しかし、これらの考え方を取り入れる上で自分たちがどこを目標として開発を進めていくのかということが明確になっていることは非常に重要なことだと考えています。

プロダクト開発の生産性が上がったり、品質保証に必要なコストが自動化などによって削減できた未来ではこのようにきめ細かい開発目標の定義とユーザーとの約束ができるようになる未来を目指していきたいとフロントエンドエンジニアとして思いました。

## 参考資料

- [Progressive Enhancement (プログレッシブエンハンスメント) - MDN Web Docs 用語集: ウェブ関連用語の定義 | MDN](https://developer.mozilla.org/ja/docs/Glossary/Progressive_Enhancement)
- [Graceful degradation (グレースフルデグラデーション) - MDN Web Docs 用語集: ウェブ関連用語の定義 | MDN](https://developer.mozilla.org/ja/docs/Glossary/Graceful_degradation)
- [Progressive Enhancement reading list 2021](https://www.quirksmode.org/blog/archives/2021/02/progressive_enh_1.html)
- [HTML: The Inaccessible Parts](https://daverupert.com/2020/02/html-the-inaccessible-parts/)
