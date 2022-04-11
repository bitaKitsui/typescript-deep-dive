# アンビエント宣言(declare)

# 得られたこと（要旨）

## Typescriptの主要な設計目標
JavaScriptライブラリを安全かつ簡単に使用できるようにすること

## アンビエント宣言とは
- これを使用することでJSライブラリを安全に使用できる
- 他のJSにコンパイルされるプロジェクトをTSに段階的に移行できる
- コンパイラとの約束であり、実行時に存在しない状態で使用しようとするとアプリが壊れる
- ドキュメントのような役割をする

## 型定義ファイル

`declare`キーワードを使用して他の場所に存在するコードを記述しようとしていることをTSに伝えることができる。

```ts
foo = 123 // Error: `foo` is not defined
```

```ts
declare let foo: any
foo = 123 // OK
```

- これらの宣言は`.ts`ファイルか`.d.ts`ファイルに記述できる
- 実際のプロジェクトでは後者を使う（`global.d.ts`など）
- `.d.ts`の場合は、各ルートレベルの定義には`declare`キーワードを前に付ける

## グローバル変数の宣言

`process変数`についてTSに伝える例

```ts
declare let process: any
```

これによって、エラーが発生することなく変数を使える。

```ts
process.exit()
```

可能な限りインターフェースを使用する。

```ts
interface Process {
    exit(code?: number): void
}
declare let process: Process
```

これにより他者が拡張しやすくなる。

```ts
interface Process {
    exitWithLogging(code?: number): void
}
process.exitWithLogging = function () {
    console.log('exiting')
    process.exit.apply(process, arguments)
}
```

# 疑問点・調査すること
特になし