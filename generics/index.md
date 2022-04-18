# ジェネリック型

# 得られたこと（要旨）

## ジェネリクスとは

メンバ間で意味のある型制約を提供するもの。メンバには以下のものがある。

- クラスのインスタンスメンバ
- クラスメソッド
- 関数の引数
- 関数の戻り値

## ジェネリクスのモチベーションとサンプル

単純な`Queue`（先入れ先出し）データ構造の実装例。

```ts
class Queue {
    private data = []
    push(item) { 
        this.data.push(item) 
    }
    pop() { 
        return this.data.shift() 
    }
}
```

この実装での問題は、キューに何でも追加できること。また、キューから要素を取り出すと、<br/>
何が出てくるかわからない。

誰かが`string`をキューにpushしているが、実際には`numbers`だけがpushされることを想定している例。

```ts
class Queue {
    private data = []
    push(item) { 
        this.data.push(item) 
    }
    pop() { 
        return this.data.shift() 
    }
}

const queue = new Queue()
queue.push(0)
queue.push("1") // mistake

console.log(queue.pop().toPrecision(1));
console.log(queue.pop().toPrecision(1)); // RUNTIME ERROR
```

解決策は、これらの制約のために特別なクラスを作成すること。

```ts
class QueueNumber extends Queue {
    push(item: number) {
        super.push(item)
    }
    pop(): number {
        return this.data.shift()
    }
}
```

文字列キューが必要な場合は、その全ての作業をもう一度行う必要がある。

本当に必要なことは、型が何であれ、pushされているものの型と、popされたものの型が同じであること。<br/>
これは、ジェネリクスパラメータで簡単に解決できる。

```ts
class Queue<T> {
    private data = []
    push(item: T) {
        this.data.push(item)
    }
    pop(): T | undefined {
        return this.data.shift()
    }
}

const queue = new Queue<number>()
queue.push(0)
queue.push("1") // ERROR : cannot push a string. Only numbers allowed
```

メンバ関数のためだけにジェネリクスを作成できる。

```ts
class Utility {
    reverse<T>(item: T[]): T[] {
        let toreturn = []
        for (let i = items.length - 1; i >= 0; i--) {
            toreturn.push(items[i])
        }
        return toreturn
    }
}
```

### デザインパターン：便利なジェネリック

```ts
declare function parse<T>(name: string): T
```

この場合、タイプTは1つの場所でのみ使用されていることがわかる。したがって、メンバ間に制約はない。<br/>
これは型安全における型アサーションに相当する。

```ts
declare function parse(name: string): any
const something = parse('something') as TypeOfSomething
```

1回だけ使用されるジェネリクスは、型安全性に関してはアサーションよりも劣っている。<br/>
それは、既に述べたように、あなたのAPIに利便性を提供するものとなっている。

```ts
const getJSON = <T>(config: {
    url: string,
    headers?: { [key: string]: string },
}): Promise<T> => {
    const fetchConfig = ({
        method: 'GET',
        'Accept': 'application/json',
        'Content-Type': 'application/json',
        ...(config.headers || {})
    })
    return fetch(config.url, fetchConfig)
        .then<T>(response => response.json())
}
```

戻り値としての`Promise<T>`は、`Promise<any>`よりも断然優れている。

次の例はジェネリクスが引数のみに使用されているもの。

```ts
declare function send<T>(arg: T): void
```

このようにすると、次のように、引数にマッチさせたい型をアノテートするのにジェネリクスTを利用できる。

```ts
send<Something>({
    x: 123,
    // Also you get autocomplete
})
```

# 疑問点・調査すること
- `getJSON`の説明部分