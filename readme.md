<p align="center">
  <b>
    ZFast
  </b>
  <br/>
  A quick, decentralized JSON store perfect for fast-moving web3 builders. <br/> <a href="https;">Redis</a> + <a href="https://ipfs.io/" target="_blank">IPFS</a> = zfast = 😍
</p>

# Install

```
git clone https://github.com/tanyaya94/zfast
npm run build
```

# Quick Start

The quickest way to test out zfast is with the zfast sandbox server (example below). If you want to run a production instance of zfast, see the [running your own server section](https://github.com/zdenham/redis-ipfs#running-your-own-ZFast-server).

```javascript
...

// See encryption section below on how to enable encryption
const myJson = { hello: 'world' };
await ZFast.set('myJsonKey', myJson, { encrypt: false });

...

const { data } = await ZFast.get('myJsonKey');

...

// reclaims memory, but preserves data on IPFS
await ZFast.purge('myJsonKey');

```

## Demo

See the speed for yourself on the zfast [demo site](https://zdenham.github.io/redis-ipfs).

# Motivation

The SQL database is a powerful tool unrivalled for storing and querying relational data.

**BUT**, I believe most dapps can live without a traditional SQL database in favor of something more light weight and decentralized.

Here are some problems with setting up a DB for your dapp

1. There are already many great services which index blockchain data (why reinvent the wheel?)
2. DBs are expensive to host
3. Migrations are often a pain
4. They tend to be centralized / treasure troves for attackers
5. They usually don't give users provenance over their data
6. Encryption / key management is a pain

Many web3 developers choose to store their data as JSON directly on IPFS rather than a traditional DB, but IPFS upload times can be slow and gateway timeouts are brutal.

If a traditional DB seems overkill for your use case and interacting directly with IPFS seems a bit too slow, zfast might be for you.

It gives you the speed of in memory-cache (Redis) but the decentralization of a global network (Filecoin + IPFS), with some other goodies like encryption.

# E2E Encryption

zfast offers easy E2E encryption for user data via [lit protocol](https://litprotocol.com). In order to use encryption, you will need to install the `lit` javascript SDK.

#### Via Package Manager

```ssh
> npm install lit-js-sdk
```

#### Via Script Tag

```html
<script
  onload="LitJsSdk.litJsSdkLoadedInALIT()"
  src="https://jscdn.litgateway.com/index.web.js"
></script>
```

### Using Encryption with zfast Client

```javascript
const ripServerUrl = 'https://zfast-sandbox.onrender.com';

// Enable encryption during client initialization
// zfast will automatically use lit-js-sdk for encryption
const zfast = new RipClient({ ripServerUrl, enableEncryption: true });

...

// you need a one-time wallet signature to
// prove identity for encryption and decryption
await zfast.signMessageForEncryption()

...

// make sure to pass encrypt: true option
const myJson = { hello: 'Encrypted zfast world' };
await zfast.set('myJsonKey', myJson, { encrypt: true });

...

// zfast automatically decrypts
const { data } = await zfast.get('myJsonKey');
```

# Running your own zfast Server

For now, if you want to use zfast in production, you will need to run your own zfast server instance.

### Instructions for hosting zfast Server

If you want to run a zfast instance on your own follow the below instructions

#### Prep

1. Install and run redis (documentation [here](https://redis.io/docs/getting-started). You can run on your local or a cloud service of your choice.
2. Acquire an NFT Storage API Key (found [here](https://nft.storage/manage))
3. Clone the repo `git clone https://github.com/zdenham/redis-ipfs.git`

#### Install Dependencies

```ssh
> yarn install
> cd server
> yarn install
```

#### Set Environment Variables:

**NOTE:** you can see the redis URI syntax [here](https://github.com/lettuce-io/lettuce-core/wiki/Redis-URI-and-connection-details)

Under `/server` directory if you are using a .env file.

```
REDIS_URL=redis://[[username:]password@]host[:port][/database]
IPFS_KEY=[YOUR_NFT STORAGE_KEY]
```

Under your environment variables you will need to set IPFS

#### Start The Server

```ssh
> yarn start
```

#### Point Your zfast Client to the Server

Once your server is live, you can point your zfast client to the server by initializing it with `ripServerUrl` which points to your server instance URL.
