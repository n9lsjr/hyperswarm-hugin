# hyperswarm-hugin

A fork of [hyperswarm](https://github.com/holepunchto/hyperswarm) with added connection signature verification for Hugin Messenger.

```
npm install hyperswarm-hugin
```

## What's different?

This fork adds signature verification to swarm connections, preventing unauthorized peers even if topics are leaked:

- **`sig`** - Signature proving ownership of shared group keys
- **`dht_keys`** - Keychain providing DHT keypair
- **`keychain`** - Shared keychain for signature verification

Uses [hyperdht-hugin](https://github.com/n9lsjr/hyperdht-hugin) under the hood.

## Usage

```js
const Hyperswarm = require("hyperswarm-hugin")

// 1. Create a keychain with a common private key for a group
// 2. Generate a random keypair (dht_keys)
// 3. Sign dht_keys.publicKey with the common keypair

const swarm = new Hyperswarm({}, sig, dht_keys, keychain)

swarm.on("connection", (conn, info) => {
  // Only verified peers can connect
  conn.write("hello verified peer")
})

const topic = Buffer.alloc(32).fill("hello world")
const discovery = swarm.join(topic, { server: true, client: true })
await discovery.flushed()
```

## API

Extended constructor:

```js
new Hyperswarm(options, sig, dht_keys, keychain)
```

| Parameter | Description |
|-----------|-------------|
| `options` | Standard hyperswarm options (maxPeers, firewall, etc.) |
| `sig` | Signature proving group membership |
| `dht_keys` | Keychain providing DHT keypair via `.get()` |
| `keychain` | Keychain for verifying signatures via `.get().verify()` |

### Key Methods

| Method | Description |
|--------|-------------|
| `swarm.join(topic, opts)` | Join a topic (server/client mode) |
| `swarm.leave(topic)` | Leave a topic |
| `swarm.joinPeer(publicKey)` | Direct connect to a peer |
| `swarm.flush()` | Wait for pending connections |
| `swarm.destroy()` | Close the swarm |

### Events

| Event | Description |
|-------|-------------|
| `connection` | New verified peer connected |
| `update` | Internal state changed |
| `ban` | Peer was banned |

For full API documentation, see the original [hyperswarm docs](https://github.com/holepunchto/hyperswarm).

## Credits

Based on [hyperswarm](https://github.com/holepunchto/hyperswarm) by [Holepunch](https://holepunch.to) (MIT License).

## License

MIT
