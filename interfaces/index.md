# インターフェース

# 得られたこと（要旨）

## インターフェースとは
- JavaScriptランタイム上では、何の影響もない
- オブジェクトの構造を宣言するための多くの機能がある
- 拡張に対してオープンである

次の2つは同じ意味の宣言になる。

```ts
// Sample A
declare let myPoint: { x: number; y: number; }

// Sample B
interface Point {
    x: number
    y: number
}
declare let myPoint: Point
```

Sample Bの場合は簡単に`myPoint`の宣言を拡張して、<br />
新しいプロパティを追加できることが良い。

```ts
// Lib a.d.ts
interface Point {
    x: number
    y: number
}
declare let myPoint: Point

// Lib b.d.ts
interface Point {
    z: number
}

let myPoint.z
```

## クラスはインターフェースを実装できる

クラスで利用する場合、互換性を保証するために`implements`キーワードを使う。

```ts
interface Point {
    x: number
    y: number
}

class MyPoint implements Point {
    x: number
    y: number
}
```

`implements`したクラスがあるとき、その外部の`Point`インターフェースを変更すると、<br />
コードベースでコンパイルエラーになるので、簡単に同期を取ることができる。

```ts
interface Point {
    x: number
    y: number
    z: number // New member
}

class MyPoint implements Point { // Error : missing member `z`
    x: number
    y: number
}
```

`implements`はクラスのインスタンスの構造を制限する。

```ts
let foo: Point = new MyPoint()
```

## すべてのインターフェースが簡単に実装できるとは限らない

JavaScriptで可能などんな構造でも宣言できるように設計されている。<br />
`new`をコールできる何かのインターフェースの例。

```ts
interface Crazy {
    new(): {
        hello: number
    }
}

class CrazyClass implements Crazy {
    constructor() {
        return { 
            hello: 123
        }
    }
}

const crazy = new CrazyClass()() // crazy would be { hello: 123 }
```

変な構造を宣言することができるが、TSのクラスとして実装できるとは限らない。

# 疑問点・調査すること
- `interface`と`type`の型宣言の違いについて