# 型推論

# 得られたこと（要旨）

## 変数の定義

変数の型は、定義によって推論される

```ts
let foo = 123 // foo is a `number`
let bar = 'Hello' // bar is a `string`
foo = bar // Error: cannot assign `string` to a `number`
```

## 関数の戻り値の型

戻り値の型は、return文によって推論される

```ts
function add(a: number, b: number) {
    return a + b // `number`を返す
}
```

## 代入

関数のパラメータ/戻り値の型は、代入によっても推論できる。<br/>
`foo`は`Adder`で、`number`は`a`と`b`の型になる。

```ts
type Adder = (a: number, b: number) => number
let foo: Adder = (a, b) => a + b
```

下記ではエラーが発生する。

```ts
type Adder = (a: number, b: number) => number
let foo: Adder = (a, b) => {
    a = 'hello' // Error: cannot assign `string` to a `number`
    return a + b
}
```

## 構造化

上記のルールは、構造化（オブジェクトリテラルの作成）の下でも機能する。

```ts
let foo = {
    a: 123,
    b: 456
}
// foo.a = "hello"; // Would Error: cannot assign `string` to a `number`
```

## 分解

構造の分解でも機能する。

```ts
let foo = {
    a: 123,
    b: 456
}
let { a } = foo
// a = "hello"; // Would Error: cannot assign `string` to a `number`
```

## `noImplicitAny`

フラグ`noImplicitAny`は、変数の型を推測できない場合、エラーを発生させるようにコンパイラに指示する。
- 明示的に`:any`型の注釈を加えることで`any`型にしたい
- 正しいアノテーションを追加することによってコンパイラを助ける

# 疑問点・調査すること
特になし