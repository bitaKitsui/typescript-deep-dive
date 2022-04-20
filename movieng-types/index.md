# 型の移動

# 得られたこと（要旨）

## Type + Valueの両方をコピーする

クラスを移動したい場合は、次の操作を行うことができる。

```ts
class Foo {}
let Bar = Foo
let bar: Bar // Error
```

`let`は`Foo`を変数宣言空間にコピーしただけなので、型の注釈として`Bar`を使うことができない。<br/>
正しい方法は、`import`キーワードを使うこと。namespacesやmodulesを使用している場合にのみ、`import`キーワードをこのように使用することができる。

```ts
namespace importing {
    export class Foo {}
}

import Bar = importing.Foo
let bar: Bar // OK
```

## 変数の型を取り込む

`typeof`演算子を使用して、型の注釈で実際に変数を使用することができる。<br/>
これにより、ある変数が別の変数と同じ型であることをコンパイラに伝えることができる。

```ts
let foo = 123
let bar: typeof foo
bar = 456
bar = '789' //Error
```

## クラスメンバのタイプを取得する

変数の型を取り込むのと同様に、単に型取得の目的で変数を宣言する。

```ts
class Foo {
    foo: number
}

declare let _foo: Foo

let bar: typeof _foo.foo // `bar` has type `number`
```

## マジック文字列の種類を取得する

多くのJSライブラリは、生のJS文字列を処理する。`const`変数を使用してその型を取り込むことができる。

```ts
const foo = 'Hello World'

let bar: typeof foo

bar = 'Hello World' // OK
bar = 'anything else' // Error
```

## キー名のキャプチャ

`keyof`演算子を使うと、ある型のキー名を取得できる。

```ts
const colors ={
    red: 'red',
    blue: 'blue'
}
type Colors = keyof typeof colors

let color: Colors
color = 'red' // OK
color = 'blue' // OK
color = 'anything else' // Error
```

# 疑問点・調査すること
特になし