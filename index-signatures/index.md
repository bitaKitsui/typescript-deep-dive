# インデックス型

# 得られたこと（要旨）

## TypeScriptのインデックス型

TypeScriptでは`toString`の誤用などではエラーを出す。

```ts
let obj = {
  toString(){
    return 'Hello'
  }
}

let foo:any = {};

// ERROR: the index signature must be string, number ...
foo[obj] = 'World';

// FIX: TypeScript forces you to be explicit
foo[obj.toString()] = 'World';
```

TypeScriptのインデックス型は`string`または`number`でなければならない。

## インデックス型を宣言する

例えば文字列を使ってオブジェクトに格納されているものが`{ message: string }`であることを確認したい場合、<br/>
`{ [index: string]: { message: string } }`の宣言で行うことができる。

```ts
let foo:{ [index:string] : {message: string} } = {};

/**
 * Must store stuff that conforms to the structure
 */
/** Ok */
foo['a'] = { message: 'some message' };
/** Error: must contain a `message` or type string. You have a typo in `message` */
foo['a'] = { messages: 'some message' };

/**
 * Stuff that is read is also type checked
 */
/** Ok */
foo['a'].message;
/** Error: messages does not exist. You have a typo in `message` */
foo['a'].messages;
```

## すべてのメンバは`string`インデックスシグネチャに従わなければならない

`string`インデックスシグネチャを持つと、それと同時に、すべての明示的なメンバもそのインデックス型に準拠する必要がある。

```ts
/** Okay */
interface Foo {
  x: number;
  y: number;
}
/** Error */
interface Bar {
  x: number;
  y: string; // ERROR: Property `y` must be of type number
}
```

## 限られた個数の文字列リテラルを使う

マップ型を使うことで、インデックス文字列が「リテラル文字型のユニオン型」のメンバであることを要求できる。

```ts
type Index = 'a' | 'b' | 'c'
type FromIndex = { [k in Index]?: number }

const good: FromIndex = { b: 1, c: 2 }

const bad: FromIndex = { b: 1, c: 2, d: 3 }
```

## `string`と`number`インデクサの両方を持つ

一般的な使用例ではないが下記もサポートしている。

```ts
interface ArrStr {
  [key: string]: string | number; // Must accommodate all members

  [index: number]: string; // Can be a subset of string indexer

  // Just an example member
  length: number;
}
```

## デザインパターン: ネストされたインデックスシグネチャ

JSライブラリにおけるCSSの共通パターンなどで文字列インデクサの濫用が見られる。

```ts
interface NestedCSS {
  color?: string;
  [selector: string]: string | NestedCSS;
}

const example: NestedCSS = {
  color: 'red',
  '.subclass': {
    color: 'blue'
  }
}
```

こうすると、例えばオブジェクトに入れるときのタイプミスがキャッチされなくなる。

```ts
const failsSilently: NestedCSS = {
  colour: 'red', // No error as `colour` is a valid string selector
}
```

代わりに、ネストを独自のプロパティに分離する。

```ts
interface NestedCSS {
  color?: string;
  nest?: {
    [selector: string]: NestedCSS;
  }
}

const example: NestedCSS = {
  color: 'red',
  nest: {
    '.subclass': {
      color: 'blue'
    }
  }
}

const failsSilently: NestedCSS = {
  colour: 'red', // TS Error: unknown property `colour`
}
```

## インデックスシグネチャから特定のプロパティを除外する

プロパティを結合してインデックス型にしたいこともある。しかし、これは推奨されていないため、<br/>
上記のネストされたインデックス型のパターンを使用するべき。

ただし、既存のJavaScriptをモデリングしている場合、交差型で回避することができる。

```ts
type FieldState = {
  value: string
}

type FormState = {
  isValid: boolean  // Error: Does not conform to the index signature
}
```

エラーの回避策。

```ts
type FieldState = {
  value: string
}

type FormState =
  { isValid: boolean }
  & { [fieldName: string]: FieldState }
```

ただし、TypeScriptでは生成できないことに注意。

# 疑問点・調査すること
特になし