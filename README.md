# Xibang Redis

[![github](https://img.shields.io/github/followers/willin.svg?style=social&label=Followers)](https://github.com/willin) [![npm](https://img.shields.io/npm/v/@xibang/redis.svg)](https://npmjs.org/package/@xibang/redis) [![npm](https://img.shields.io/npm/dt/@xibang/redis.svg)](https://npmjs.org/package/@xibang/redis) [![codecov](https://codecov.io/gh/xibang/node-redis/branch/master/graph/badge.svg)](https://codecov.io/gh/xibang/node-redis) [![codebeat badge](https://codebeat.co/badges/bf602cde-5250-4d0d-9a96-54df3dd291fd)](https://codebeat.co/projects/github-com-xibang-node-redis-master) [![Build Status](https://travis-ci.org/xibang/node-redis.svg?branch=master)](https://travis-ci.org/xibang/node-redis)

## 安装

```
yarn add @xibang/redis
```

## ES7 使用

```js
const redis = require('@xibang/redis');

const client = redis({
  host: '127.0.0.1',
  port: 6379,
  db: 0
});

(async()=>{
  // 直接用默认的数据库
  console.log(await client.get('test'));

  // 或者选择数据库
  const db1 = client.select(1);
  console.log(await db1.get('test'));
})();
```

注意：

```js
(async()=>{
  // Select DB将会返回实例
  client.select(1);
  // 这里依然用的默认的 db（可能是db0，根据配置，默认0）进行操作
  console.log(await client.get('test'));
})();
```

## Subscribe / Publish

```js
const redis = require('@xibang/redis');

// 需要不同的配置以保证连接不被复用。
const sub = redis({ test: 1 }).client;
const pub = redis({ test: 2 }).client;

let msgCount = 0;

sub.on('subscribe', () => {
  pub.publish('a nice channel', 'I am sending a message.');
  pub.publish('a nice channel', 'I am sending a second message.');
  pub.publish('a nice channel', 'I am sending my last message.');
});

sub.on('message', (channel, message) => {
  console.log(`sub channel ${channel}: ${message}`);
  msgCount += 1;
  if (msgCount === 3) {
    sub.unsubscribe();
    sub.quit();
    pub.quit();
  }
});

sub.subscribe('a nice channel');
```

## License

MIT

通过支付宝捐赠：

![qr](https://cloud.githubusercontent.com/assets/1890238/15489630/fccbb9cc-2193-11e6-9fed-b93c59d6ef37.png)
