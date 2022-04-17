# 型ガード

# 得られたこと（要旨）

## 型ガードでできること
条件ブロック内のオブジェクトの型を制限できる

## typeof

条件付きブロックでtypeofを使用すると、TypeScriptはその条件ブロック内で異なる変数の型を理解する。

```ts
function doSomething(x: number | string) {
    if (typeof x === 'string') {
        // ...
    }
}
```

## instanceof

クラスの場合、`instanceof`で理解させられる。

```ts
class Foo {
    foo = 123
    common = '123'
}

class Bar {
    bar = 123
    common = '123'
}

function doStuff(arg: Foo | Bar) {
    if (arg instanceof Foo) {
        // ...
    }
    if (arg instanceof Bar) {
        // ...
    }
}

doStuff(new Foo())
doStuff(new Bar())
```

## in

`in`演算子は、オブジェクト上のプロパティの存在を安全にチェックし、型ガードとして使用できる。

```ts
interface A {
    x: number
}

interface B {
    y: string
}

function doStuff(q: A | B) {
    if ('x' in q) {
        // q: A
    } else {
        // q: B
    }
}
```

## リテラル型のType Guard

異なるリテラル値を区別するには、`===` / `==` / `!==` / `!=`が利用できる。<br />
ユニオン型の中にリテラル型がある場合にも同じ方法が使える。

```ts
type Foo = {
    kind: 'foo'
    foo: number
}

type Bar = {
    kind: 'bar'
    bar: number
}

function doStuff(arg: Foo | Bar) {
    if (arg.kind === 'foo') {
        // arg.foo
    } else {
        // arg.bar
    }
}
```

## `strictNullChecks`を使用したnullとundefinedのチェック

`== null` / `!= null`チェックすることで`null`と`undefined`の両方を排除できる。

```ts
function foo(a?: number | null) {
    if (a == null) return
    // a is number now.
}
```

## Type Guardsとコールバック

TypeScriptは、コールバック内で型ガードがアクティブのままであることが危険なため、想定していない。

```ts
declare let foo: { bar?: { baz: string } }
function immediate(callback: () => void) {
    callback()
}

// Type Guard
if (foo.bar) {
    console.log(foo.bar.baz) // OK
    functionDoingSomeStuff(() => {
        console.log(foo.bar.baz) // TS error: Object is possibly 'undefined'"
    })
}
```

下記のように修正すると正しく動作する。

```ts
// Type Guard
if (foo.bar) {
  console.log(foo.bar.baz); // Okay
  const bar = foo.bar;
  functionDoingSomeStuff(() => {
    console.log(bar.baz); // Okay
  });
}
```

# 疑問点・調査すること
特になし