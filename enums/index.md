# インターフェース

# 得られたこと（要旨）

## 列挙型(Enums)とは
- 関連する値の集合を編成する方法
- 他のプログラミング言語には`enum`データ型を持っているが、JSにはない
- TSには存在する

```ts
enum CardSuit {
    Clubs,
    Diamonds,
    Hearts,
    Spades
}

// Sample usage
let card = CardSuit.Clubs

// Safety
card = "not a member of card suit" // Error : string is not assignable to type `CardSuit`
```

## 数値列挙型と数値(Number Enums and Numbers)

TS列挙型は数値ベース。これは、数値を列挙型のインスタンスに割り当てることができることを意味し、<br />
`number`と互換性のあるもの。

```ts
enum Color {
    Red,
    Green,
    Blue
}
let col = Color.Red
col = 0 // Effectively same as Color.Red
```

## 数値列挙型と文字列(Number Enums and Strings)

ここでのサンプル。

```ts
enum Tristate {
    False,
    True,
    Unknown
}
```

JSとして下記が生成される。

```js
var Tristate;
(function (Tristate) {
    Tristate[Tristate["False"] = 0] = "False";
    Tristate[Tristate["True"] = 1] = "True";
    Tristate[Tristate["Unknown"] = 2] = "Unknown";
})(Tristate || (Tristate = {}));
```

`Tristate`変数で、列挙型の文字列バージョンを数値または数値バージョンに変換できることを意味する。

```ts
enum Tristate {
    False,
    True,
    Unknown
}
console.log(Tristate[0]) // "False"
console.log(Tristate["False"]) // 0
console.log(Tristate[Tristate.False]) // "False"
```

## 数値列挙型に関連付けられた数値を変更する

デフォルトでは列挙型は「0」に基づいており、その後の各値は自動的に1ずつ増加する。

```ts
enum Color {
    Red,     // 0
    Green,   // 1
    Blue     // 2
}
```

任意の列挙型メンバに番号を関連付けることもできる。

```ts
enum Color {
    DarkRed = 3,  // 3
    DarkGreen,    // 4
    DarkBlue      // 5
}
```

## フラグとしての数値列挙型(Number Enums as flags)

- 列挙型の優れた使い方は、フラグとして使用すること
- 一連の条件から特定の条件が真であるかどうかを確認できる

```ts
enum AnimalFlags {
    None           = 0,
    HasClaws       = 1 << 0,
    CanFly         = 1 << 1,
    EatsFish       = 1 << 2,
    Endangered     = 1 << 3
}
```

## 文字列列挙型(String Enums)

文字列型を持つ列挙型メンバも作れる。

```ts
export enum EvidenceTypeEnum {
  UNKNOWN = '',
  PASSPORT_VISA = 'passport_visa',
  PASSPORT = 'passport',
  SIGHTED_STUDENT_CARD = 'sighted_tertiary_edu_id',
  SIGHTED_KEYPASS_CARD = 'sighted_keypass_card',
  SIGHTED_PROOF_OF_AGE_CARD = 'sighted_proof_of_age_card',
}
```

これらは意味を持つためデバッグしやすくなる。<br />
簡単な文字列の比較をする例。

```ts
// Where `someStringFromBackend` will be '' | 'passport_visa' | 'passport' ... etc.
const value = someStringFromBackend as EvidenceTypeEnum;

// Sample use in code
if (value === EvidenceTypeEnum.PASSPORT){
    console.log('You provided a passport');
    console.log(value); // `passport`
}
```

## 定数列挙型(Const Enums)

下記の例では、`var lie = Tristate.False`はコンパイル時に入力と同じになる。<br />
つまり、実行時にランタイムは`Tristate`と`Tristate.False`を検索する必要が生じる。

```ts
enum Tristate {
    False,
    True,
    Unknown
}

var lie = Tristate.False;
```

パフォーマンスを向上するために、`enum`を`const enum`とする。

```ts
const enum Tristate {
    False,
    True,
    Unknown
}

var lie = Tristate.False;
```

生成されるJS。

```js
var lie = 0;
```

コンパイラが行うこと:

1. 列挙型のあらゆる用途をインライン化する（`Tristate.False`ではなく`0`）
2. enum定義用のJSを生成しない

## 静的関数を持つ列挙型(Enum with static functions)

`enum`と`namespace`を合体させて、静的メソッドを列挙型に追加できる。<br />
静的メンバ`isBusinessDay`を列挙型`Weekday`に追加する例。

```ts
enum Weekday {
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday,
    Sunday
}
namespace Weekday {
    export function isBusinessDay(day: Weekday) {
        switch (day) {
            case Weekday.Saturday:
            case Weekday.Sunday:
                return false;
            default:
                return true;
        }
    }
}

const mon = Weekday.Monday;
const sun = Weekday.Sunday;
console.log(Weekday.isBusinessDay(mon)); // true
console.log(Weekday.isBusinessDay(sun)); // false
```

### 列挙型はオープンエンド

さっきの例で生成されるJS。

```js
var Tristate;
(function (Tristate) {
    Tristate[Tristate["False"] = 0] = "False";
    Tristate[Tristate["True"] = 1] = "True";
    Tristate[Tristate["Unknown"] = 2] = "Unknown";
})(Tristate || (Tristate = {}));
```

`(Tristate || (Tristate = {}));`部分は、基本的に既に捕捉されたローカル変数`Tristate`を指し示すか、新しい空のオブジェクトで初期化するローカル変数`Tristate`を取得する。

つまり、列挙型定義を複数のファイルに分割することができる。<br />
`Color`の定義を2つのブロックに分割した例。

```ts
enum Color {
    Red,
    Green,
    Blue
}

enum Color {
    DarkRed = 3,
    DarkGreen,
    DarkBlue
}
```

注意点としては、後の`Color`定義の最初のメンバは`3`と再初期化すること

# 疑問点・調査すること
- TSの`enum`は現状非推奨だと目にしたことがあるので調査