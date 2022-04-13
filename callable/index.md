# 呼び出し可能オブジェクト

# 得られたこと（要旨）

## 呼び出し可能オブジェクトとは

型またはインターフェースの一部分としてアノテーションを付けられること。

```ts
interface ReturnString {
    (): string
}

declare const foo: ReturnString
const bar = foo()
```

## 明白な例

必要に応じて引数/オプション引数/可変長引数を指定することもできる。

```ts
interface Complex {
    (foo: string, bar?: number, ...others: boolean[]): number
}
```

インターフェースは、複数の呼び出し可能なアノテーションを提供して、関数のオーバーロードを指定できる。

```ts
interface Overloaded {
    (foo: string): string
    (foo: number): number
}

function stringOrNumber(foo: number): number
function stringOrNumber(foo: string): string
function stringOrNumber(foo: any): any {
    if (typeof foo === 'number') {
        return foo * foo
    } else if (typeof foo === 'string') {
        return `hello ${foo}`
    }
}

const overloaded: Overloaded = stringOrNumber

const str = overloaded('')
const num = overloaded(123)
```

インターフェースの本体のように、呼び出し可能オブジェクトのインターフェースの本体を変数の型名として使用できる。

```ts
const overloaded: {
    (foo: string): string
    (foo: number): number
} = (foo: any) => foo
```

## アロー構文

単純なアロー型アノテーションも使用できる。オーバーロードは指定できない。

```ts
const simple: (foo: number) => string
    = (foo) => foo.toString()
```

## ニューアブル(Newable)

接頭辞`new`を持つ特殊なコーラブル型アノテーションのこと。

```ts
interface CallMeWithNewToGetString {
    new(): string
}

declare const Foo: CallMeWithNewToGetString
const bar = new Foo()
```

# 疑問点・調査すること
- ニューアブルの使い道