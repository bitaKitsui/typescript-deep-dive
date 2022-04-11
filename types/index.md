# @types パッケージ (DefinitelyTyped)

# 得られたこと（要旨）

## DefinitelyTypedとは
- JSプロジェクトの厳密な型定義を行っているコミュニティ
- [Definitely Typed](https://github.com/DefinitelyTyped/DefinitelyTyped)

## `@types`を使う
インストールは`npm`上で簡単にできる。
```
npm install @types/jquery --save-dev
```
`@types`はグローバルとモジュールの両方の型定義をサポートする。

## グローバル`@types`
デフォルトでは、グローバルに利用する定義は自動的に包含される。<br />
ライブラリによってはモジュールを使用する例もある。

## モジュール`@types`
インストール後に特別な設定は不要。
```ts
import * as $ from "jquery"

// Use $ at will in this module :)
```

## グローバルの制御
- グローバル定義を自動許可する設定は、一部のチームでは問題になる
- `tsconfig.json`の`compilerOptions.types`を使い、必要な型だけを指定して、明示的に取り込むことが可能

```json
{
  "compilerOptions": {
    "types": [
      "jquery"
    ]
  }
}
```

上記では`jquery`だけを使用できるようになる。<br />
誰かが`@types/node`を入れても、追記しない限り使用できなくなる。

# 疑問点・調査すること
特になし