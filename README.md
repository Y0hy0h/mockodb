# MockoDB

[![npm](https://img.shields.io/npm/v/mockodb.svg)][npm]
[![build status](https://travis-ci.org/Y0hy0h/mockodb.svg?branch=master)](https://travis-ci.org/Y0hy0h/mockodb)
[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://prettier.io/)

In-memory mock for MongoDB in unit tests.

> Inspired by [mongo-unit].

## Installation

MockoDB is [available on NPM][npm].

```bash
yarn add mockodb
npm install --save mockodb
```

## Usage

```typescript
import { MockoDb } from "mockodb";
import { MongoClient } from "mongodb";

async function demo() {
  const mockoDb = await MockoDb.boot();

  // You can now connect to the database.
  const client = await MongoClient.connect(mockoDb.url.href);
  ...

  // At any time you can drop all databases.
  await mockoDb.reset();
  ...

  // You need to take care of shutting down the db.
  await mockoDb.shutdown();
}
```

You can also control individual databases.

```typescript
import { MockoDb } from "mockodb";
import { MongoClient } from "mongodb";

async function demo() {
  const mockoDb = await MockoDb.boot();
  // Open a new database with a random name.
  const dbHandle = await mockoDb.open();
  const client = await MongoClient.connect(dbHandle.url.href);
  ...

  // Reset only that database.
  await dbHandle.drop();

  await mockoDb.shutdown();
}
```

### Preloading

Note that `MockoDb.boot()` might attempt to download the MongoDB binaries on the
first run. You can preload those libraries explicitly before your tests run
using the `preload()` function:

```typescript
import { preload } from "mockodb";

describe("test suite", () => {
  beforeAll(async () => {
    jest.setTimeout(100_000); // Preload might take a while.
    await preload();
  });

  // ...
});
```

Alternatively, for a more direct use inside your CI's script, this package
exposes the `mockodb-preload` command in its bin:

```bash
yarn install
mockodb-preload
yarn test
```

### Download Directory

MockoDB will download the MongoDB binaries into its folder. Therefore you can
simply cache your `node_modules` on your CI, making it download the MongoDB
binaries once on the first run and whenever you clear your cache.

[npm]: https://www.npmjs.com/package/mockodb
[mongo-unit]: https://github.com/mikhail-angelov/mongo-unit
