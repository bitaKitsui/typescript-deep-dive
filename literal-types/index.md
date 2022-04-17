# リテラル型

# 得られたこと（要旨）

## リテラル型とは

JavaScriptのプリミティブである値そのものの型。

## 文字列リテラル

```ts
let foo: 'Hello'
```

これに代入されるリテラル値は`Hello`のみを許可する。

```ts
let foo: 'Hello'
foo = 'Bar' // Error: "Bar" is not assignable to type "Hello"
```

ユニオン型で結合して、強力な抽象化を作成することができる。

```ts
type CardinalDirection = 
    | "North"
    | "East"
    | "South"
    | "West"

function move (distance: number, direction: CardinalDirection) {
    // ...
}

move(1, "North") // OK
move(1, "Nurth") // Error
```

## その他のリテラル型

TypeScriptは`boolean`と`number`リテラル型もサポートしている。

```ts
type OneToFive = 1 | 2 | 3 | 4 | 5
type Bools = true | false
```

## 推論

下記のようなエラーがよく生じる。

```ts
function iTakeFoo(foo: 'foo') { }
const test = {
  someProp: 'foo'
};
iTakeFoo(test.someProp); // Error: Argument of type string is not assignable to parameter of type 'foo'
```

これは`test`が`{ someProp: string }`型であると推測されるため。<br />
この問題を解決するためには、シンプルな型アサーションを使用して、以下のようにリテラルを推測させる。

```ts
function iTakeFoo(foo: 'foo') { }
const test = {
  someProp: 'foo' as 'foo'
};
iTakeFoo(test.someProp); // Okay!
```

あるいは、型アノテーションを使い、宣言した時点で正しく推論するのを助けることもできる。

```ts
function iTakeFoo(foo: 'foo') { }
type Test = {
    someProp: 'foo'
}
const test: Test = {
    someProp: 'foo'
}
iTakeFoo(test.someProp) // Okay!
```

## ユースケース

### 文字列ベースの列挙型

TypeScript enumsは数字ベース。`CardinalDirection`の例のように<br />
ユニオン型の文字列リテラルを使用して文字列ベースの列挙型を模倣できる。

```ts
function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
    return o.reduce((res, key) => {
        res[key] = key
        return res
    }, Object.create(null))
}

const Direction = strEnum([
    'North',
    'South',
    'East',
    'West'
])

type Direction = keyof typeof Direction

let sample: Direction

sample = Direction.North // OK
sample = 'North' // OK
sample = 'AnythingElse' // Error
```

# 疑問点・調査すること
特になし