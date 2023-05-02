---
title: "私がthrowを使わない理由"
emoji: "😊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['javascript','typescript']
published: false
---

## 私がthrowを使わない理由

JavaScriptでは`throw`文という文を使うことで例外を投げることができます。

https://jsprimer.net/basic/error-try-catch/#throw

https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/throw

この`throw`文ですが、私はよくレビューなどで例外を投げないでください（`throw`文を使わないでほしい）というコメントをするのですが

### 前提条件

この記事の内容は下記の条件を前提として書き進めていきます。

- `TypeScript`を採用していること
- Webフロントエンド開発に`JavaScript`を利用する場合

Node.jsを利用したサーバーサイドのコードやCLIツールの開発、各種ライブラリの開発については本記事の対象に含まれないことをご了承下さい。

### 結論

先に結論から書いておくとTypeScriptを利用している場合例外はカスタムエラーを返却するか、Result型を利用するのがよいと思っています。
次の章からサンプルコードを用いながら`throw`文を使った実例と、代替え案について記述していきます。

### throw文の問題点

`throw`文を利用した場合のデメリットとして個人的に感じている観点は下記の２つです。

- `try...catch`を使うべき関数なのかという情報が外部から分からない
- 想定していない`Error`を受け取ってしまう場合がある

下記の`x`と`y`を入力してその合計値を返す`add`関数を例として考えていきます。

```ts:sample.ts
export const add = (x: number, y: number): number => {
  return x + y;
}
```

この関数に`x`か`y`どちらかが`NaN`の場合例外を投げるという使用を追加してみましょう。

```diff ts:sample.ts
 export const add = (x: number, y: number): number => {
+  if (Number.isNaN(x) || Number.isNaN(y)) {
+    throw new Error('NaNは入力値として使用できない');
+  }
   return x + y;
 }
```

これでこの関数は特定の条件で例外を投げる関数となりました。

まずは「`try...catch`を使うべき関数なのかという情報が外部から分からない」という部分ですが  
この関数を他ファイルから`import`して利用する場合型定義は下記のようになります。

```ts:sample.d.ts
export declare const add: (x: number, y: number) => number;
```

この状態では外部から利用する際に失敗する可能性のある関数（`try...catch`を利用するべき関数）であることがコード内部を確認すまで分からない状態となります。

### ErrorとのUnion型で表現する

一番とっつきやすい変更としては従来`throw new Error()`としていた箇所を`return new Error()`に書き換える手法だと思います。
これまでのサンプルコードに適用すると下記のようになります。

```diff ts
-export const add = (x: number, y: number): number => {
+export const add = (x: number, y: number): number | Error => {
   if (Number.isNaN(x) || Number.isNaN(y)) {
-    throw new Error('NaNは入力値として使用できない');
+    return new Error('NaNは入力値として使用できない');
   }
   return x + y;
 }
```

変更を加えることにより関数の返り値の型が`number`から`number|Error`に変化しました。
このように例外を投げるのではなく、エラーを返却することにより利用側では`Error`型が含まれるため失敗する可能性のある関数であることが分かります。

しかしこの方法でも「想定していない`Error`を受け取ってしまう場合がある」という問題は解決しません。

### 想定していない`Error`を受け取ってしまう場合がある

次に「想定していない`Error`を受け取ってしまう場合がある」という問題ですが、まずは最初の`throw`を使った実装から確認していきます。

```ts:sample.ts
export const add = (x: number, y: number): number => {
  if (Number.isNaN(x) || Number.isNaN(y)) {
    throw new Error('NaNは入力値として使用できない');
  }
  return x + y;
}
```

実際に利用側から`add`関数を使用してみた例が下記です。

```ts
import { add } from "./sample";
try {
  add(1, 2);
} catch {
  alert("計算できない文字列が含まれています");
}
```

このように入力が静的に決まっている場合はそもそも例外が発生しないことは明らかですが、実務のコードはより複雑になることが多いです。
例として`add`関数の引数が配列の特定の位置を抜き出すというコードの場合[`RangeError`](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/RangeError)が発生する場合があります。

https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/RangeError

```ts
import { add } from "./sample";
// ユーザーの入力値を配列に格納しておく変数
const userInput: number[] = foo;
try {
  add(userInput[0], userInput[1]);
  // userInput.length = 1 の場合などに RangeError が発生する可能性
} catch {
  alert("計算できない文字列が含まれています");
  // 実際には入力値が不足しているにもかかわらずすべてのエラーをcatchしてしまう
}
```

このように`try...catch`では全てのエラーを受け取ってしまうため、意図しないエラーも受け取って`catch`節が実行されてしまう可能性があります。

例外を投げずに`Error`との`Union`型を返却する場合もこの点には注意が必要で、`JavaScript`の各種エラーは`Error`を継承しているため`RangeError instanceof Error`は`true`になってしまいます。

```ts
import { add } from "./sample";

const userInput: number[] = foo;
const result: number | Error = add(userInput[0], userInput[1]);

if (result instanceof Error) {
  // RangeError も instanceof Errorを満たしてしまう
  alert("計算できない値が含まれています")
};
```

これを回避するためには返却するエラーを独自エラーに変更する必要があります。

```ts:sample.ts
// 計算不能を表すエラー
export class IncomputableError extends Error {
  constructor() {
    super();
  }
}

export const add = (x: number, y: number): number | IncomputableError => {
  if (Number.isNaN(x) || Number.isNaN(y)) {
    return new IncomputableError('NaNは入力値として使用できない');
  }
  return x + y;
}
```

```ts
import { add, IncomputableError } from "./sample";

const userInput: number[] = foo;
const result: number | IncomputableError = add(userInput[0], userInput[1]);

if (result instanceof IncomputableError) {
  alert("計算できない値が含まれています")
};
```

この手法の場合ランタイムでも有効なチェックができる一方で、冗長な書き方になってしまいます。
そのため`TypeScript`を利用している場合はタグ付きユニオンを利用したResult型を利用することでよりスマートな書き方ができます。

### Result型で表現する

`Result`型とは、
