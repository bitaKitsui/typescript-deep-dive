# mixins

# 得られたこと（要旨）

TypeScriptクラスは、厳密な単一継承をサポートしている。

再使用可能なコンポーネントからクラスを構築する別の方法は、mixinと呼ばれる、より単純な部分クラスを組み合わせてそれらを構築すること。

> [mixinは]
> - コンストラクタをとり、
> - コンストラクタを拡張し、新しい機能を持つクラスを作成する
> - 新しいクラスを返す

## コンストラクタを取る

ミックスインはクラスを拡張し、新しい機能を追加する。したがって、コンストラクタを定義する必要がある。

```ts
// Needed for all mixins
type Constructor<T = {}> = new (...args: any[]) => T;
```

## クラスを拡張して返す

```ts
// A mixin that adds a property
function Timestamped<TBase extends Constructor>(Base: TBase) {
  return class extends Base {
    timestamp = Date.now();
  };
}
```

# 疑問点・調査すること

- mixinの使い所