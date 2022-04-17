# Freshness

# 得られたこと（要旨）

## Freshnessとは？

オブジェクトリテラルや構造的に互換性がある型の型チェックを簡単にする概念。

構造型を用いて、型安全性のレベルを維持しながらJSをTSにアップグレードしている例。

```ts
function logName(something: { name: string }) {
    console.log(something.name)
}

let person = { name: 'matt', job: 'being awesome' }
let animal = { name: 'cow', diet: 'vegan, but has milk of own species' }
let random = { note: `I don't have a name property` }

logName(person)
logName(animal)
logName(random) // Error: property `name` is missing
```

構造型の入力は、余計なものを含むデータを入れるとエラーが出てしまう。

```ts
function logName(something: { name: string }) {
    console.log(something.name);
}

logName({ name: 'matt' }); // okay
logName({ name: 'matt', job: 'being awesome' }); // Error: object literals must only specify known properties. `job` is excessive here.
```

## 追加のプロパティを許可する

型には、追加のプロパティが許可されていることを明示的に示すインデックスシグネチャを含めることができる。

```ts
let x: {
    foo: number
    [x: string]: unknown
}

x = {
    foo: 1,
    baz: 2
}
```

# 疑問点・調査すること
特になし