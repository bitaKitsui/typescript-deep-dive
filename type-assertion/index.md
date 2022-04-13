# Type Assertion（型アサーション）

# 得られたこと（要旨）

## Type Assertionとは

TSが推論、分析された型が、任意の方法で上書きできる方法のメカニズムのこと。<br />
一般的な使用例は、JSからTSへ移植する場合。

```ts
var foo = {};
foo.bar = 123; // Error: property 'bar' does not exist on `{}`
foo.bas = 'hello'; // Error: property 'bas' does not exist on `{}`
```

`foo`の推論された型が`{}`、すなわちプロパティがゼロのオブジェクトだからエラーが出る。<br />
単純に型アサーションで修正が可能。

```ts
interface Foo {
    bar: number;
    bas: string;
}
var foo = {} as Foo;
foo.bar = 123;
foo.bas = 'hello';
```

## `as foo`と`<foo>`の違い

JSXで`<foo>`を使用すると文法に曖昧さが生まれるため、`as foo`の利用がおすすめ。

```ts
var foo: any;
var bar = <string> foo; // bar is now of type "string"

// JSXの例
var foo = <string>bar;
</string>
```

## 型アサーションとキャスト

「型キャスト」と呼ばれない理由は、キャストとは一般的に何らかのランタイムサポートを意味するから。<br />
一方、型アサーションは純粋にコンパイル時の構造体であり、コードをどのように解析するかについてのヒントをコンパイラに提供する方法。

## アサーションは害

多くの場合、アサーションを使用すると、レガシーコードを簡単に移行できる。<br />
しかし、アサーションの使用には注意が必要。

```ts
interface Foo {
    bar: number
    bas: string
}
let foo = {} as Foo
// 必要なプロパティを忘れてもコンパイルは通る
```

別の一般的な考え方として、autocompleteを提供する手段としてアサーションは使用される。

```ts
interface Foo {
    bar: number
    bas: string
}
let foo = <Foo>{
    // 追加を忘れる可能性がある
    // Fooが改修されると、こちらも直さなくてはならない
}
```

## ダブルアサーション

逆に型アサーションが有用な例もある。

例えばユーザーが渡されたイベントは特定ケースのイベントだと考える例。

```ts
function handler (event: Event) {
    let mouseEvent = event as MouseEvent
}
```

ただし、次のようなエラーが発生する可能性が高い。

```ts
function handler(event: Event) {
    let element = event as HTMLElement; // Error: Neither 'Event' nor type 'HTMLElement' is assignable to the other
}
```

その型が必要な場合はダブルアサーションを使うことができる。

```ts
function handler(event: Event) {
    let element = event as any as HTMLElement; // Okay!
}
```

## TypeScriptが単一のアサーションが不十分と判断する方法

基本的に、`S`が`T`のサブタイプであるか`T`が`S`のサブタイプである場合、<br />
`S`から`T`へのアサーションは成功する。

# 疑問点・調査すること
特になし