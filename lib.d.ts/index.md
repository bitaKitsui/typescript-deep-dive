# lib.d.ts

# 得られたこと（要旨）

## `lib.d.ts`とは
特別な宣言ファイル`lib.d.ts`はTypeScriptをインストールしたときに付属している。<br />
このファイルにはJSランタイムとDOMに存在する様々な一般的なJSを構成する機能の`declare`が含まれている。

- このファイルは、TSプロジェクトのコンパイルテキストに自動的に含まれる
- このファイルの目的は、JSやTSのプロジェクトで、すぐに型のサポートを得られるようにすること
- TSをサポートしているIDEであれば、JSのプロジェクトであっても、型の恩恵を得られる

コンパイルオプションに`--noLib`を指定してこのファイルをコンパイルテキストから除外することもできる。

## 使用例

```ts
let foo = 123
let bar = foo.toString()
```

すべてのJSオブジェクトに対して`toString`関数が`lib.d.ts`で定義されているため、<br />
問題なく型チェックされる。

```ts
let foo = 123
let bar = foo.toString() // ERROR: Property 'toString' does not exist on type 'number'.
```

`noLib`オプションで同じコードを使うと、型チェックエラーが発動する。

## `lib.d.ts`の内容

- 主に変数宣言の集まりとなっている。
- `window`, `document`, `math`など
- 同様のインターフェース宣言`Window`, `Document`, `Math`など

`window`の定義例。

```ts
declare var window: Window
```

TypeScriptのおかげで`Window`の型情報などを頭で覚えておく必要がない。

## ネイティブ型(Native Types)を変更する

TSの`interface`はオープンエンドなので、`lib.d.ts`で宣言されたインターフェースに<br />
メンバを追加するだけで、TSはその追加を認識してくれる。

これらのインターフェースを`lib.d.ts`に関連付けるために、変更をグローバルモジュールで行う必要がある。<br />
このために、`global.d.ts`という特別なファイルを作成することがおすすめ。

### `window`の例

```ts
interface Window {
    helloWorld(): void
}
```

```ts
// Add it at runtime
window.helloWorld = () => console.log('hello world');
// Call it
window.helloWorld();
// Misuse it and you get an error:
window.helloWorld('gracius'); // Error: Supplied parameters do not match the signature of the call target
```

### `Math`の例

```ts
interface Math {
    seedrandom(seed?: string)
}
```

```ts
Math.seedrandom()
or
Math.seedrandom('something')
```

### `string`の例

`String`インターフェースは、文字列リテラルに対しても影響がある。

```ts
interface String {
    endsWith(suffix: string): boolean;
}

String.prototype.endsWith = function(suffix: string): boolean {
    var str: string = this;
    return str && str.indexOf(suffix, str.length - suffix.length) !== -1;
}

console.log('foo bar'.endsWith('bas')); // false
console.log('foo bas'.endsWith('bas')); // true
```

### 独自のカスタム`lib.d.ts`を使用する

`--noLib`のコンパイラフラグを使用すると、TSは自動的にこのファイルを除外する。<br />
これが有用な理由は一般的に下記となる。

- 標準ブラウザベースのランタイム環境とは大きく異なるカスタムJavaScript環境で実行する場合
- コード内で使用可能なグローバルを厳密に制御したい場合

デフォルトの`lib.d.ts`を除外すると、コンパイルテキストに同様の名前のファイルを含めることができ、<br />
TSは型チェックのためにそれを取り込むことになる。

### コンパイラターゲットの`lib.d.ts`に対する効果

ターゲットを`es6`に設定すると`lib.d.ts`は`Promise`のようなより現代的な環境宣言を追加する。

### libオプション

場合によっては、コンパイルターゲットと環境ライブラリサポートの関係を切り離したい場合がある。<br />
`lib`コンパイラオプションを使い明示的に制御することができる。

<strong>tsconfig.json</strong>

```json
"compilerOptions": {
    "lib": ["dom", "es6"]
}
```

libは以下のように分類できる。

- JavaScriptのバルク機能
  - es5
  - es6
  - es2015
  - es7
  - es2016
  - es2017
  - esnext
- 実行時環境
  - dom
  - dom.iterable
  - webworker
  - scripthost
- ESNext By-Featureオプション
  - es2015.core
  - es2015.collection
  - es2015.generator
  - es2015.iterable
  - es2015.promise
  - es2015.proxy
  - es2015.reflect
  - es2015.symbol
  - es2015.symbol.wellknown
  - es2016.array.include
  - es2017.object
  - es2017.sharedmemory
  - esnext.asynciterable

## 古いJavaScriptエンジン用のポリフィル

`Map`/`Set`や、`Promise`のような、いくつかのランタイム機能は、モダンな`lib`オプションで使用できるものがかなりある。
これらを使うには`core-js`を使うだけ。

```
npm install core-js --save-dev
```

```ts
import "core-js"
```

# 疑問点・調査すること
特になし