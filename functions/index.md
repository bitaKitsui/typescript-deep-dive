# 関数の型

# 得られたこと（要旨）

## パラメータの型アノテーション

関数のパラメータにアノテーションを付けることができる。

```ts
let sampleVariable: { bar: number }

function foo (sampleParameter: { bar: number }) { }
```

## 戻り値の型アノテーション

戻り値の型にも関数パラメータリストの後にアノテーションを付けることができる。

```ts
interface Foo {
    foo: string
}

function foo (sample: Foo): Foo {
    return sample
}
```

コンパイラが型推論できる場合は、戻り値の型を省略できる。

```ts
interface Foo {
    foo: string
}

function foo (sample: Foo) {
    return sample
}
```

一般的には、これらのアノテーションを追加してエラー対処をしやすくするのが良い。

```ts
function foo () {
    return { fou: 'John Doe' } // fooのスペルミスに気づけない
}

sendAsJSON(foo())
```

## オプションパラメータ

パラメータをオプションとしてマークすることができる。

```ts
function foo(bar: number, bas?: string): void {
    // ..
}

foo(123)
foo(123, 'hello')
```

パラメータ宣言の後に`= someValue`を使用してデフォルト値を提供することもできる。<br />
これは、呼び出し元がその引数を提供しない場合に設定される。

```ts
function foo(bar: number, bas: string = 'hello') {
    console.log(bar, bas)
}

foo(123) // 123, hello
foo(123, 'world') // 123, world
```

## オーバーロード

関数のオーバーロードを宣言できる。これは、ドキュメントと型の安全性を高める目的に役立つ。

```ts
function padding(a: number, b?: number, c?: number, d?: any) {
    if (b === undefined && c === undefined && d == undefined) {
        b = c = d= a
    } else if (c === undefined && d === undefined) {
        c = a
        d = b
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    }
}
```

上記の関数の引数は1つ、2つ、または4つとなる。これらの制約は、関数のオーバーロードを使用して<br />
強制と文書化を行うことができる。

```ts
// オーバーロード
function padding(all: number)
function padding(topAndBottom: number, leftAndRight: number)
function padding(top: number, right: number, bottom: number, left: number)

function padding(a: number, b?: number, c?: number, d?: number) {
    if (b === undefined && c === undefined && d == undefined) {
        b = c = d= a
    } else if (c === undefined && d === undefined) {
        c = a
        d = b
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    }
}
```

最初の3つの関数ヘッダは`padding`への有効な呼び出しとして利用できる。

```ts
padding(1)
padding(1, 1)
padding(1, 1, 1, 1)

padding(1, 1, 1) // オーバーロードに無いためエラー
```

## 関数の宣言

具体的な実装を示さずに関数の型を宣言する方法には、2つある。

```ts
type LongHand = {
    (a: number): number
}

type ShortHand = (a: number) => number
```

前者の`LongHand`の書き方の場合のみオーバーロードを追加できる。

```ts
type LongHandAllowsOverloadDeclarations = {
    (a: number): number
    (b: string): string
}
```

# 疑問点・調査すること
特になし