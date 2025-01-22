---
title: "「stateはスナップショットである」をバグから理解する"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react"]
published: false
---

::: message
タイトルのキャッチーさのために「バグ」という言葉を利用してしまいましたが
ここで述べられているReactの挙動は正しいものでありタイトルで利用した「バグ」という表現は **「実装者が意図していなかった挙動」** のことを指します。
:::

## サンプルコードと問題

まずはこのコードをご覧ください。

```tsx:Counter.tsx
import { useState, useEffect, type FC } from "react";

const Counter:FC = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const handler = (event: KeyboardEvent) => {
      if (event.key === 'Enter') {
        setCount(count + 1);
      };
      console.log(count);
    };

    window.addEventListener('keydown', handler);

    return (() => window.removeEventListener('keydown', handler));
  }, []);

  return (
    <div>Counter</div>
  )
};
```

このコードでやってることを簡単にまとめると下記のようになります。

- `count`という`state`を定義
- Enterキーが押下される度に`state`を+1するイベントハンドラーを定義
- `useEffect`によってコンポーネントの初回レンダリング時にイベントハンドラーを設定

イベントハンドラー内にある`console.log()`では呼び出される度に`count`を出力します。
コンポーネントが描画されている状態で`Enter`を3回押すとどのようなログになるでしょうか。

::: details 回答
![Chrome開発者ツールのスクリーンショット、コンソールタブに3回0が出力されている](/images/articles/useeffect-with-event-handler/on-enter-console-log.png)
*`0`が3回出力されています。*

今回のコードではこのように何度Enterを押してもコンソールには常に`0`が出力されます。
:::

実際に挙動を確認してみたい人は下記の埋め込みから確認できます。

@[codesandbox](https://codesandbox.io/embed/ly75kf?expanddevtools=1)

## state はスナップショットである

Reactの公式ドキュメントにも記載があるように、stateは動的な変数というよりコンポーネントの呼び出し時には常に同じ値を指す定数のような扱いに近いです。

https://ja.react.dev/learn/state-as-a-snapshot
