# never

# 得られたこと（要旨）

## never

プログラミング言語の設計には、bottom型の概念がある。それは、データフロー解析を行うと現れるもの。
TypeScriptはデータフロー解析を実行するので、決して起こり得ないようなものを確実に表現する必要がある。

`never`型は、このbottom型を表すためにTypeScriptで使用される。

- 絶対にreturnされない関数（例えば関数本体に`while(true){}`がある場合）
- 常にthrowする関数

neverはneverだけを代入できる。

```ts
let foo: never = 123 // Error

let bar: never = (() => {
    throw new Error('Error')
})() // OK
```

## ユースケース: 網羅チェック

たどり着けないコンテキストで関数を呼び出すことはできないが、<br/>
`never`は他の`never`にのみ割り当てられるため、コンパイル時の網羅チェックのために使うことができる。

## `void`との混同

何も返さない関数は`void`を返す。しかし、returnを返すことのない関数は`never`を返す。<br/>
`void`は代入することができるが、`never`は`never`以外のものに代入できない。

# 疑問点・調査すること
- 網羅チェックの意味