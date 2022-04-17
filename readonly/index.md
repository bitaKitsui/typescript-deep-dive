# Readonly

# 得られたこと（要旨）

## readonly

インターフェース上の個々のプロパティを`readonly`としてマークできる。<br />
これにより、関数的に作業することができる。

```ts
function foo(config: {
    readonly bar: number
    readonly bas: number
}) {
    // ...
}

let config = { bar: 123, bas: 123 }
foo(config)
```

`interface`と`type`の定義に`readonly`を使うこともできる。

```ts
type Foo = {
    readonly bar: number
    readonly bas: number
}

let foo: Foo = { bar: 123, bas: 456 }

foo.bar = 456 // Error
```

## Readonly

`Readonly`型は`T型`をとり、そのすべてのプロパティを`readonly`とマークする。

```ts
type Foo = {
    bar: number
    bas: number
}

type FooReadonly = Readonly<Foo>

let foo:Foo = { bar: 123, bas: 456 };
let fooReadonly:FooReadonly = { bar: 123, bas: 456 };

foo.bar = 456; // Okay
fooReadonly.bar = 456; // ERROR: bar is readonly
```

### さまざまな使用例

#### シームレスな不変性(Seamless Immutable)

インデックスのシグネチャをreadonlyとしてマークすることができる。

```ts
interface Foo {
    readonly [x: number]: number
}

let foo: Foo = { 0: 123, 2: 345 }
console.log(foo[0]) // OK
foo[0] = 456 // Error
```

```ts
let foo: ReadonlyArray<number> = [1, 2, 3]
console.log(foo[0]) // OK
foo.push(4) // Error
foo = foo.concat([4]) // OK
```

### `const`との相違点

`const`
1. 変数参照に利用するものである
2. 他の値を再度割り当てることはできない

`readonly`
1. プロパティに利用するものである
2. エイリアシングによってプロパティが変更されることがありえる

基本的に`readonly`は、プロパティを変更できないことを保証するが、<br />
その保証を持たない人にそれを渡すと変更ができてしまう。

# 疑問点・調査すること
特になし