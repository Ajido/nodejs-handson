# Minimum Hands-on Node.js

- [Speaker Deck - Minimum Hands-on Node.js](https://speakerdeck.com/ajido/minimum-handson-nodejs)
- [演習リンク](https://chouseisan.com/s?h=4a0d7eb9c38a440e9ad585abfbb16e79)

## P5 インストール

[nodejs.org](https://nodejs.org/en/) からインストールします。LTS と Current のうち、長期間のメンテナンスが保証され、可能な限り互換性が維持される LTS バージョンを選択してください。

## P6 演習テスト

ターミナルで Node.js のバージョンを表示してください。

```bash
$ node -v
```

今回のハンズオンは Node.js の v12.x でなければなりません。バージョンが確認できたら、

1. 演習リンクのページを開く
2. 「出欠を入力する」を押す
3. 今回は演習テストなので「演習テスト」の項目を × から ○ に変更する
4. 「入力する」を押して、演習を完了する

https://chouseisan.com/s?h=4a0d7eb9c38a440e9ad585abfbb16e79

## P7 標準モジュール

Node.js は Google Chrome に採用されている V8 JavaScript エンジンと、標準の JavaScript にはない各種実装が組み合わせられた JavaScript の実行環境です。ここでの標準モジュールとは、Node.js に組み込まれた「標準の JavaScript にはない各種実装」のことを指します。

https://nodejs.org/dist/latest/docs/api/documentation.html

```js
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  console.log(data)
})
```

## P9 REPL

Node.js の対話的インターフェースです。REPL（Read-Eval-Print-Loop）環境では標準モジュールが自動的にロードされるため、それらを読み込むことなく扱えます。コードのすばやい検証に役立ちますので、演習中も是非活用してみてください。

## P10 演習 1

自身の端末の CPU コアの数を、標準モジュールと REPL を使って表示してください。

```bash
$ node
```

REPL の終了は `<Ctrl>-D` を送るか `.exit` と入力してください。その他コマンドは `.help` を参照

## P12 npm

npm (Node Package Manager) は npm, Inc.により管理・提供されている Node.js 組み込みのパッケージ管理システムです。世界中から利用される中央レジストリの npmjs.com には多数のモジュールが公開されています。

```bash
$ npm init
$ npm install request 
```

偶発的なパッケージの公開を防ぐために `package.json` に `private: true` を追記してください。このオプションを有効にすることで、本来 `package.json` に記述しなければならない各種情報を省略することもできます。

## P13 演習 2

`npm init` コマンドで `package.json` を作成したあと `request` モジュールをインストールしてください。その後 `request` モジュールを使って HTTP リクエストを送り、ステータスコードを表示してください。

```bash
$ npm init
## echo '{"private":true}' > package.json
$ npm install request
```

```js
const request = require('request')

request('https://www.yahoo.co.jp', (err, res, body) => {
  console.log(res.statusCode)
})
```

## P15 モジュール管理ワークフロー

```bash
## package.json作成
$ npm init

## モジュールの追加 / package-lock.jsonからnode_modulesの復元
$ npm install <name> / npm ci

## 脆弱性チェック
$ npm audit
$ npm audit fix

## パッケージの更新チェック
$ npm outdated

## SemVerベースのパッケージ更新
$ npm update <name>

## SemVerに依存しないパッケージ更新
$ npm install <name>@<version>
```

## P24 コールバック

```js
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  console.log(data.toString())
})
```

## P26 コールバックのエラーハンドリング

```js
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  if (err) {
    console.error(err)
    return
  }

  console.log(data)
})
```

## P27 コールバックのよくある失敗例

`return` の書き忘れ

```js
// 誤
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  if (err) console.error(err)
  console.log(data)
})
```

```js
// 正
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  if (err) return console.error(err)
  console.log(data)
})
```

## P28 コールバックのよくある失敗例

`try/catch` を使って非同期処理のエラーを捕まえる

```js
// 誤
const fs = require('fs')

try {
  fs.readFile(__filename, (err, data) => {
    console.log(data)
  })
} catch (err) {
  console.error(err)
}
```

```js
// 正
const fs = require('fs')

fs.readFile(__filename, (err, data) => {
  if (err) return console.error(err)
  console.log(data)
})
```

## P29 コールバックのよくある失敗例

非同期処理の同期的なループ

```js
// 誤
const fs = require('fs')

for (let i = 0; i < 100; i++) {
  fs.appendFile('./data.txt', i + ',', (err) => {})
}

// 0,3,4,5,6,7,8,9,10,11,12,20,21,1,2,28,3,4,5,6,7,8...
```

```js
// 再帰処理による対処の一例（必ずしも正解というわけではありません）
const write = (i) => {
  if (i === 100) return
  fs.appendFile('./data.txt', i + ',', (err) => {
    write(++i)
  })
}

write(0)
```

## P31 イベント駆動

```js
const Redis = require('ioredis')
const redis = new Redis()

const stream = redis.scanStream({
  match: '*',
  count: 100
})

// 約100件のキーを読み込むごとに呼び出される
stream.on('data', (keys) => {
  for (const key of keys) {
    console.log(key)
  }
})

// 全てのキーを走査した後に呼び出される
stream.on('end', () => {
  console.log(‘end')
})
```

## P32 イベント駆動のエラーハンドリング

```js
const Redis = require('ioredis')
const redis = new Redis()

const stream = redis.scanStream({
  match: '*',
  count: 100
})

// 約100件のキーを読み込むごとに呼び出される
stream.on('data', (keys) => {
  for (const key of keys) {
    console.log(key)
  }
})

// 全てのキーを走査した後に呼び出される
stream.on('end', () => {
  console.log('end')
})

// 何かしらエラーが発生した時に呼び出される
stream.on('error', (err) => {
  console.error(err)
})
```

## P33 イベント駆動のよくある失敗例

`on('error')`  のハンドリング漏れとそれに伴うプロセスのクラッシュ

```js
// 誤
const fs = require('fs')
const rs = fs.createReadStream(__filename)

rs.on('data', (data) => {
  console.log(data)
})
```

```js
// 正
const fs = require('fs')
const rs = fs.createReadStream(__filename)

rs.on('data', (data) => {
  console.log(data)
})

rs.on('error', (err) => {
  console.error(err)
})
```

## P36 同期処理のエラーハンドリング

```js
try {
  // JSON.parseの例外処理は特に忘れがちなので注意してください
  const data = JSON.parse('invalid_json')
} catch (err) {
  console.error(err)
}
```

```js
try {
  const data = fs.readFileSync('invalid_path')
} catch (err) {
  console.error(err)
}
```

## P37 チェックポイント

- Node.js は LTS バージョンを利用する
- モジュールの利用と管理方法を理解する
- 非同期処理を駆使してイベントループの停止時間を最小限に抑える
- コールバックとイベント駆動のデザインを理解する
- コールバックとイベント駆動のエラーハンドリング方法を理解する
- 同期処理のエラーハンドリング方法を理解する

## P41 コールバックによるフロー制御

```js
// 再帰処理による非同期処理のループ
const fs = require('fs')

const write = (i) => {
  if (i === 100) return

  fs.appendFile('./data.txt', `${i},`, (err) => {
    write(++i)
  })
}

write(0)
```

## P43 async/await

```js
const fs = require('fs').promises

const main = async () => {
  for (let i = 0; i < 100; i++) {
    await fs.appendFile('./data.txt', `${i},`)
  }
}

main()
```

## P45 演習 3

下記コールバックパターンの再帰処理を `async/await` を使った処理に変換してください。

```js
const fs = require('fs')

const write = (i) => {
  if (i === 100) return

  fs.appendFile('./data.txt', `${i},`, (err) => {
    write(++i)
  })
}

write(0)
```

## P46 イベント駆動のフロー制御

```js
const Redis = require('ioredis')
const redis = new Redis()

const main = async () => {
  const stream = redis.scanStream({
    match: '*',
    count: 100
  })

  try {
    for await (const keys of stream) {
      // 約100件のキーを読み込むごとに呼び出される
      for (const key of keys) {
        console.log(key)
      }
    }

    // 全てのキーを走査した後に呼び出される
    console.log('end')
  } catch (err) {
    // 何かしらエラーが発生した時に呼び出される
    console.error(err)
  }
}

main()
```

## P47 演習 4

下記イベント駆動パターンのコードを `for await` を使った処理に変換してください。

```js
const fs = require('fs')
const rs = fs.createReadStream(__filename)

rs.on('data', (data) => {
  console.log(data.toString())
})

rs.on('error', (err) => {
  console.error(err)
})
```

## P48 async/await のエラーハンドリング

```js
const fs = require('fs').promises

const main = async () => {
  for (let i = 0; i < 100; i++) {
    try {
      await fs.appendFile('./data.txt', `${i},`)
    } catch (err) {
      console.error(err)
    }
  }
}

main().catch((err) => {
  console.error(err)
})
```

## P49 async/await のよくある失敗例

- `await` の書き忘れ
- 不要な Promise の利用
- `.catch` の書き忘れ（エラーハンドリングの記述漏れ）

```js
const fs = require('fs').promises

const main = async () => {
  for (let i = 0; i < 100; i++) {

    // await fs.appendFile('./data.txt', `${i},`)
    // const stat = await fs.stat('./data.txt')
    const stat = fs.appendFile('./data.txt', `${i},`).then(() => {
      return fs.stat(‘./data.txt’)
    })

    console.log(stat)
  }

  // return 'done'
  return Promise.resolve('done')
}

// .catch((err) => console.error(err))
main().then((result) => console.log(result))
```

## P50 async/await を使った包括的なエラーハンドリング

```js
const main = async () => {
  JSON.parse('invalid_json')
}

main().catch((err) => {
  console.error(err)
})

// SyntaxError:
//  Unexpected token b in JSON at position 0
//  at JSON.parse (<anonymous>)
```

## P51 チェックポイント

- `async/await` の扱い方を理解する
- `async/await` のエラーハンドリング方法を理解する
- 可読性と安全性を考慮し、可能な限り `async/await` ファーストの設計を心掛ける
