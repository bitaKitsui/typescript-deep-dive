# JavaScriptからの移行ガイド

# 得られたこと（要旨）

## 移行プロセスは一般的に下記
- `tsconfig.json`を追加する
- 拡張子を`.js`から`.ts`に変え、any型を使ってエラーを抑制する
- 古いコードに戻り、型アノテーションを追加し、バグを修正する
- サードパーティ製JSコードの型定義を使用する

## エラーを抑制する
移行中のエラーの多くは`any`を使用して抑制ができる。

```ts
let foo = 123
let bar = 'hey'

bar = foo // Type 'number' is not assignable to type 'string'.
```

エラー回避の例

```ts
let foo = 123
let bar = 'hey'

bar = foo as any
```

関数にアノテーションをする例

```ts
function foo() {
    return 1
}

let bar = 'hey'
bar = foo() // Type 'number' is not assignable to type 'string'.
```

```ts
function foo(): any {
    return 1
}

let bar = 'hey'
bar = foo()
```

`any`でエラー抑止をした場合は、`// TODO:`コメントを残すのが無難。

## サードパーティ製JavaScript

初めから全てTypeScriptを使用することは難しい。<br />
`vendor.d.ts`など宣言ファイルを作成し、色々と雑多に詰め込むことがお勧め。

### `jquery`の例
```ts
declare let $: any
```

明示的にアノテーションを追加する例
```ts
declare type JQuery = any
declare let $: JQuery
```

これによって、将来の更新が簡単になる。

## サードパーティー製NPMモジュール

グローバル変数宣言と同様に、グローバルモジュールを簡単に宣言できる。

### `jquery`をモジュールとして使用する例
```ts
declare module "jquery"
```

必要に応じてインポートできる。
```ts
import * as $ from "jquery"
```

## 外部の非jsリソース

JS以外のファイルでも、単純な`*`スタイルの宣言でインポートできる。

### `.css`の例
理想としては`global.d.ts`でインポートする
```ts
declare module "*.css"
```

これで`import * as foo from "./some/file.css"`とインポート可能。

# 疑問点・調査すること
特になし。