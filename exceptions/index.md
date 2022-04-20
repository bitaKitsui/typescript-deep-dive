# 例外のハンドリング

# 得られたこと（要旨）

## エラーサブタイプ

ビルトインされた`Error`クラスのほかに、JSランタイムが投げることができる`Error`を継承するいくつかの組み込みエラークラスがある。

### RangeError

数値変数またはパラメータが有効な範囲外にあるときに発生するエラーを表すインスタンスを作成する。

```ts
console.log.apply((console, new Array(100000000))) // RangeError: Invalid array length
```

### ReferenceError

参照されたのが無効な参照だった場合に発生するエラーを表すインスタンスを作成する。

```ts
'use strict'
console.log(notValidVar) // ReferenceError: notValidVar is not defined
```

### SyntaxError

有効でないJSを解析する際に発生する構文エラーを表すインスタンスを作成する。

```ts
1***3; // SyntaxError: Unexpected token *
```

### TypeError

変数またはパラメータが有効な型でないときに発生するエラーを表すインスタンスを作成する。

```ts
('1.2').toPrecision(1); // TypeError: '1.2'.toPrecision is not a function
```

### URIError

`encodeURI()`または`decodeURI()`に無効なパラメータが渡されたときに発生するエラーを表すインスタンスを作成する。

```ts
decodeURI('%'); // URIError: URI malformed
```

## 常にエラーを使用する。

初心者の開発者は、たまに生の文字列のみをエラーとして投げるが、これはしてはいけない。<br/>
`Error`オブジェクトの基本的な利点は、`stack`プロパティを使ってビルドされた場所を自動的に追跡できることにある。

## 例外的なケース

### どこに投げられるのか不明

どの関数がエラーを投げるかわからない。

```ts
try {
  const foo = runTask1();
  const bar = runTask2();
}
catch(e) {
  console.log('Error:', e);
}
```

## 優雅なハンドリングを困難にする

スローする可能性のあるものの周りを明示的にキャッチして優雅にしようとすることができる。

```ts
try {
  const foo = runTask1();
}
catch(e) {
  console.log('Error:', e);
}
try {
  const bar = runTask2();
}
catch(e) {
  console.log('Error:', e);
}
```

しかし、最初のタスクから2番目に物事を渡す場合、コードが乱雑になる。

```ts
let foo: number; // Notice use of `let` and explicit type annotation
try {
  foo = runTask1();
}
catch(e) {
  console.log('Error:', e);
}
try {
  const bar = runTask2(foo);
}
catch(e) {
  console.log('Error:', e);
}
```

## 型システムでうまく表現されていない

```ts
function validate(value: number) {
  if (value < 0 || value > 100) throw new Error('Invalid value');
}
```

このような場合に`Error`を使うのは、validate関数の型定義で表現されていないので良くない。

代わりに、検証メソッドを作成するための良い方法は以下

```ts
function validate(value: number): {error?: string} {
  if (value < 0 || value > 100) return {error:'Invalid value'};
}
```

# 疑問点・調査すること
特になし