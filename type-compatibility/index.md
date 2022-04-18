# 型の互換性

# 得られたこと（要旨）

## 型の互換性

型の互換性は、あるものを別のものに割り当てることができるかどうかを決定する。<br/>
例えば`string`と`number`は互換性がない。

## 健全性

TypeScriptの型システムは便利であるように設計されているため、不健全な振る舞いをすることも可能にしている。<br/>
例えば、何かを`any`型にすることができるなど。

## 構造的

TypeScriptオブジェクトは構造的に型付けされている。つまり、型名は構造が一致する限り重要ではない。

```ts
interface Point {
    x: number
    y: number
}

class Point2D {
    constructor(public x: number, public y: number) {}
}

let p: Point
p = new Point2D(1, 2)
```

これにより、素早くオブジェクトを作成することができ、推論が可能な場合でも安全性が保たれる。

## バリアンス

型の互換性分析のために理解しやすく、重要な概念のこと。

単純な型`Base`と`Child`の場合、`Child`が`Base`の子であれば、`Child`のインスタンスは`Base`型の変数に代入できる。

このような`Base`型と`Child`型で構成される複合型の型互換性は、類似のシナリオにおける`Base`と`Child`がバリアンスによって制御されることに依存している。

## 関数

2つの関数を比較するときには、いくつか些細なことを考慮する必要がある。

### 戻り値の型

戻り値の型は少なくとも十分なデータを含んでなければならない。

### 引数の数

引数が少ないことは問題ない。少なくとも十分な引数で呼び出されることが保証されている。

### オプションおよび可変長パラメータ

オプションパラメータ（?）および可変長パラメータ（[]）は、利便性のために互換性がある。

### 引数のタイプ

一般的なイベント処理のシナリオをサポートするように設計されている。

## 列挙型

- 列挙型は数値と互換性があり、数値は列挙型と互換性がある
- 異なる列挙型のEnum値は互換性がないとみなされる。これによって、列挙型をnominalに使うことができる

## クラス

- インスタンスメンバとメソッドのみが比較される。コンストラクタと静的メンバに関しては何もしない。
- `private`と`protected`メンバは同じクラスで書かれたものでなければならない。そのようなメンバは、本質的にそのクラスをnominalにする。

### ジェネリクス

TypeScriptは構造的な型システムを備えているため、型パラメータはメンバが使用するときの互換性にのみ影響する。<br/>
例えば、以下の`T`は互換性に影響を与えない。

```ts
interface Empty<T>{}

let x: Empty<number>
let y: Empty<string>

x = y // okay, y matches structure of x
```

しかし、`T`を使用すると、以下に示すようにインスタンス化に基づいて互換性チェックの働きをする。

```ts
interface NotEmpty<T> {
    data: T;
}
let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // error, x and y are not compatible
```

ジェネリック引数がインスタンス化されていない場合、それらは互換性チェックする前に`any`で置き換えられる。

```ts
let identity = function<T>(x: T): T {
    // ...
}

let reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

クラスを含むジェネリックは、前述のように関連するクラスの互換性によってマッチされる。

```ts
class List<T> {
  add(val: T) { }
}

class Animal { name: string; }
class Cat extends Animal { meow() { } }

const animals = new List<Animal>();
animals.add(new Animal()); // Okay 
animals.add(new Cat()); // Okay 

const cats = new List<Cat>();
cats.add(new Animal()); // Error 
cats.add(new Cat()); // Okay
```

# 疑問点・調査すること
- nominalの意味