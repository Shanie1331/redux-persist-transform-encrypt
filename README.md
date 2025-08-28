# @shanie1331/redux-persist-transform-encrypt

> A maintained fork of [`redux-persist-transform-encrypt`](https://github.com/maxdeviant/redux-persist-transform-encrypt), updated because the original has been archived.  
> This fork fixes modern build/export issues, ships **ESM + CJS** bundles, and includes **TypeScript typings**.

[![npm](https://img.shields.io/npm/v/@shanie1331/redux-persist-transform-encrypt.svg)](https://www.npmjs.com/package/@shanie1331/redux-persist-transform-encrypt)

Encrypt your Redux store when persisting with `redux-persist`.

---

## Why this fork?

The original library is archived and may cause:

- `Cannot find module` / `Failed to resolve the path`
- Export/interop issues (`import` vs `require`)
- TypeScript errors

This fork is drop-in compatible and actively maintained.

---

## Installation

Requires `redux-persist@^6`.

```bash
# npm
npm install @shanie1331/redux-persist-transform-encrypt redux-persist

# yarn
yarn add @shanie1331/redux-persist-transform-encrypt redux-persist

# pnpm
pnpm add @shanie1331/redux-persist-transform-encrypt redux-persist
```

---

## Usage

### ESM

```ts
import { persistReducer } from 'redux-persist';
import storage from 'redux-persist/lib/storage'; // or AsyncStorage in RN
import { encryptTransform } from '@shanie1331/redux-persist-transform-encrypt';
import rootReducer from './rootReducer';

const persistConfig = {
  key: 'root',
  storage,
  transforms: [
    encryptTransform({
      secretKey: 'my-super-secret-key',
      onError(error) {
        console.error('[persist-encrypt] error:', error);
      },
    }),
  ],
};

export default persistReducer(persistConfig, rootReducer);
```

### CommonJS

```js
const { persistReducer } = require('redux-persist');
const storage = require('redux-persist/lib/storage').default;
const {
  encryptTransform,
} = require('@shanie1331/redux-persist-transform-encrypt');

const persistConfig = {
  key: 'root',
  storage,
  transforms: [
    encryptTransform({
      secretKey: process.env.SECRET_KEY,
      onError: err => console.error(err),
    }),
  ],
};

module.exports = persistReducer(persistConfig, require('./rootReducer'));
```

### React Native (AsyncStorage)

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import { persistReducer } from 'redux-persist';
import { encryptTransform } from '@shanie1331/redux-persist-transform-encrypt';
import rootReducer from './rootReducer';

const persistConfig = {
  key: 'root',
  storage: AsyncStorage,
  transforms: [
    encryptTransform({
      secretKey: '<per-user-secret-here>',
      onError: e => console.log(e),
    }),
  ],
};

export default persistReducer(persistConfig, rootReducer);
```

---

## API

### `encryptTransform(options)`

| Option    | Type                     | Required | Description                                                                 |
| --------- | ------------------------ | -------- | --------------------------------------------------------------------------- |
| secretKey | `string`                 | yes      | Passphrase used to derive a 256‑bit AES key.                                |
| onError   | `(error: Error) => void` | no       | Handle encryption/decryption errors (wipe storage, logout, telemetry, etc.) |

> Internally uses `crypto-js` for AES and `json-stringify-safe` for robust serialization.

---

## Security notes: picking a secret

- **Do not hardcode** one global secret for all users.
- Prefer **per‑user, deterministic secrets** provided by your backend (e.g., derived from user id + server salt) behind auth.
- For session-only persistence, a session/access token can be acceptable.
- If decryption fails, use `onError` to wipe the persisted state and rehydrate cleanly.

---

## Output & Types

This fork publishes bundled dual builds:

- ESM entry: `lib/index.js`
- CJS entry: `lib/index.cjs`
- Types: `lib/index.d.ts`

Works with Node, Webpack, Vite, and React Native toolchains.

---

## Troubleshooting

- **“Cannot find module …/lib/index.cjs or index.js”**  
  Ensure you installed the **scoped** fork: `@shanie1331/redux-persist-transform-encrypt`.

- **ESM error like “Cannot find module './sync.js'”**  
  Old multi-file ESM output caused this. This fork ships **bundled single-file** entries for both ESM and CJS.

- **Decryption error on app start**  
  Your `secretKey` changed (user switched, old cache). Handle via `onError` and clear persisted storage.

---

## Migration from the archived package

1. Replace imports:
   ```diff
   - import { encryptTransform } from 'redux-persist-transform-encrypt'
   + import { encryptTransform } from '@shanie1331/redux-persist-transform-encrypt'
   ```
2. Keep only one transform instance; order can matter relative to other transforms.
3. If your keying strategy changed, expect one-time wipe of persisted state via `onError`.

---

## Contributing

PRs and issues are welcome. Please include a minimal repro when reporting interop problems.

---

## License

MIT © Shashank Malviya
