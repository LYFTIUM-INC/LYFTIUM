# LYFTIUM

LYFTIUM is an Ethereum Mainnet JSON-RPC endpoint priced in requests per minute (rpm), not compute units.

- **Website:** https://www.lyftium.com
- **Portal:** https://app.lyftium.com
- **Endpoint:** `https://eth-mainnet-rpc.lyftium.com`
- **Network:** Ethereum Mainnet only

## Why LYFTIUM

- **Pricing you can read.** Each plan has a requests-per-minute limit. You do not have to convert methods into compute units or credits to estimate your bill.
- **Honest tip gate.** When the node's sync gate is closed, the endpoint returns HTTP `503` instead of serving a stale chain tip. Your client can fail over or retry rather than act on old data.
- **Status (https://app.lyftium.com/status) is the source of truth.** LYFTIUM Status reports whether the tip is ready. Check Status before you conclude anything about availability.

## Quick start

1. Sign up and create an API key at https://www.lyftium.com.
2. Send JSON-RPC requests with your key in the `X-Api-Key` header.

```bash
curl -s https://eth-mainnet-rpc.lyftium.com \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $LYFTIUM_API_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"eth_blockNumber","params":[]}'
```

### Authentication

The API key is accepted **only** in the `X-Api-Key` header. Do not put your key in the URL or a query string; keys in query strings are not supported.

### ethers.js (v6)

```js
import { ethers } from "ethers";

const req = new ethers.FetchRequest("https://eth-mainnet-rpc.lyftium.com");
req.setHeader("X-Api-Key", process.env.LYFTIUM_API_KEY);

const provider = new ethers.JsonRpcProvider(req, 1, { staticNetwork: true });
console.log(await provider.getBlockNumber());
```

### viem

```js
import { createPublicClient, http } from "viem";
import { mainnet } from "viem/chains";

const client = createPublicClient({
  chain: mainnet,
  transport: http("https://eth-mainnet-rpc.lyftium.com", {
    fetchOptions: { headers: { "X-Api-Key": process.env.LYFTIUM_API_KEY } },
  }),
});

console.log(await client.getBlockNumber());
```

## Handling `503` (tip gate closed)

LYFTIUM fails closed. If the sync gate is closed, requests return HTTP `503`. Treat `503` as "do not trust this tip right now":

- Retry with backoff, or route to a fallback provider.
- Check LYFTIUM Status for the current tip state.
- Do not cache a `503` as an empty or valid result.

## Pricing

| Plan       | Price      | Rate limit       | Billing            |
|------------|------------|------------------|--------------------|
| Standard   | $29 / mo   | 100 rpm          | Polar checkout     |
| Premium    | $99 / mo   | 500 rpm          | Polar checkout     |
| Enterprise | $499 / mo  | Custom rpm       | Invoice            |

- Checkout runs on **Polar**.
- **USDC** is accepted only when the checkout lists it as an option, and only on **Ethereum Mainnet**.
- There is no free tier.

## Scope

- Supported: Ethereum Mainnet.
- Not supported: other chains, L2s, or testnets.

## Support

Manage keys, plans, and billing at https://app.lyftium.com.
