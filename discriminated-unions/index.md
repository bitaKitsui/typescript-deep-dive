# 判別可能なUnion型

# 得られたこと（要旨）

## Union型の判別

literal型のメンバを持つクラスがある場合、そのプロパティを使用して、Union型のメンバを判別することができる。

`Square`と`Rectangle`のUnionの例

```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
type Shape = Square | Rectangle;
```

判別用のプロパティ（ここでは`kind`）に対して、型安全なチェックまたは`switch`を使用すると、<br/>
TypeScriptはそのリテラル型を持つオブジェクトの型を特定し、型の絞り込みを行う。

```ts
function area(s: Shape) {
    if (s.kind === 'square') {
        return s.size * s.size
    } else {
        return s.width * s.width
    }
}
```

## 網羅チェック

例えばユニオンに新たな値が追加された場合。

```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

// Someone just added this new `Circle` Type
// We would like to let TypeScript give an error at any place that *needs* to cater for this
interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Square | Rectangle | Circle;
```

`else`を追加し、そのブロックの推論された型が`never`型と互換性があるかを確認する。

```ts
function area(s: Shape) {
    if (s.kind === "square") {
        return s.size * s.size;
    } else if (s.kind === "rectangle") {
        return s.width * s.height;
    } else if (s.kind === "circle") {
        return Math.PI * (s.radius **2);
    } else {
        // Okay once more
        const _exhaustiveCheck: never = s;
    }
}
```

## `Switch`

`switch`ステートメントでも同様のことができる。

```ts
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default: const _exhaustiveCheck: never = s;
    }
}
```

## strictNullChecks

strictNullChecksを使用して網羅チェック中にエラーが出る場合、`_exhaustiveCheck`変数を返す必要がある。

```ts
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default:
          const _exhaustiveCheck: never = s;
          return _exhaustiveCheck;
    }
}
```

## 網羅チェックの中で例外を投げる

引数として`never`を取る関数を書くことができる。そして、次のように関数の本体が実行された場合に例外を投げることができる。

```ts
function assertNever(x:never): never {
    throw new Error('Unexpected value. Should have been never.');
}
```

## Retrospective Versioning

次のような型のデータ構造があるとする。

```ts
type DTO = {
    name: string
}
```

`DTO`を様々な場所で使用したあとに、`name`という名前は良くない選択だったことに気付くとする。<br/>
このような場合には、リテラルの数値を追加したDTOの新しいユニオン型を定義することで、バージョニングを追加できる。

```ts
type DTO = 
| { 
   version: undefined, // version 0
   name: string,
 }
| {
   version: 1,
   firstName: string,
   lastName: string, 
}
// Even later 
| {
    version: 2,
    firstName: string,
    middleName: string,
    lastName: string, 
} 
// So on
```

# 疑問点・調査すること
特になし