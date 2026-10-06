# LYFTIUM

Ethereum Mainnet JSON-RPC priced in **requests per minute (rpm)** — not compute units.

[Website](https://www.lyftium.com) · [Portal](https://app.lyftium.com) · [Status](https://app.lyftium.com/status) · [Endpoint](https://eth-mainnet-rpc.lyftium.com)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Network: Ethereum Mainnet](https://img.shields.io/badge/Network-Ethereum%20Mainnet-3C3C3D?logo=ethereum&logoColor=white)](https://www.lyftium.com)
[![Auth: X-Api-Key](https://img.shields.io/badge/Auth-X--Api--Key%20header-0A7EA4)](https://app.lyftium.com)

## Table of contents

- [Why LYFTIUM](#why-lyftium)
- [Quick start](#quick-start)
- [Client examples](#client-examples)
- [Handling 503 (tip gate closed)](#handling-503-tip-gate-closed)
- [Pricing](#pricing)
- [Rate limits](#rate-limits)
- [FAQ](#faq)
- [Scope](#scope)
- [Support](#support)

## Why LYFTIUM

- **Pricing you can read.** Each plan has a requests-per-minute limit. You do not have to convert methods into compute units or credits to estimate your bill.
- **Honest tip gate.** When the node's sync gate is closed, the endpoint returns HTTP `503` instead of serving a stale chain tip. Your client can fail over or retry rather than act on old data.
- **[Status](https://app.lyftium.com/status) is the source of truth.** LYFTIUM Status reports whether the tip is ready. Check Status before you conclude anything about availability.

## Quick start

1. Sign up and create an API key at [https://www.lyftium.com](https://www.lyftium.com).
2. Send JSON-RPC requests with your key in the `X-Api-Key` header **only** (no query string).

```bash
curl -s https://eth-mainnet-rpc.lyftium.com \
  -H "Content-Type: application/json" \
  -H "X-Api-Key: $LYFTIUM_API_KEY" \
  -d '{"jsonrpc":"2.0","id":1,"method":"eth_blockNumber","params":[]}'
```

### Authentication

The API key is accepted **only** in the `X-Api-Key` header. Do not put your key in the URL or a query string; keys in query strings are not supported.

## Client examples

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

### web3.py

```python
import os
from web3 import Web3

w3 = Web3(
    Web3.HTTPProvider(
        "https://eth-mainnet-rpc.lyftium.com",
        request_kwargs={
            "headers": {"X-Api-Key": os.environ["LYFTIUM_API_KEY"]},
        },
    )
)
print(w3.eth.block_number)
```

## Handling `503` (tip gate closed)

LYFTIUM fails closed. If the sync gate is closed, requests return HTTP `503`. Treat `503` as "do not trust this tip right now":

- Retry with backoff, or route to a fallback provider.
- Check [LYFTIUM Status](https://app.lyftium.com/status) for the current tip state.
- Do not cache a `503` as an empty or valid result.

## Pricing

| Plan       | Price     | Rate limit | Billing        |
|------------|-----------|------------|----------------|
| Standard   | $29 / mo  | 100 rpm    | Polar checkout |
| Premium    | $99 / mo  | 500 rpm    | Polar checkout |
| Enterprise | $499 / mo | Custom rpm | Invoice        |

- Checkout runs on **Polar**.
- **USDC** is accepted only when the checkout lists it as an option, and only on **Ethereum Mainnet**.
- There is **no free tier**.

## Rate limits

Plan limits are **requests-per-minute (rpm) caps**. For current usage, keys, and plan details, use the [portal](https://app.lyftium.com). If you need higher limits or have questions about overage behavior, contact support through the portal.

## FAQ

**How is rpm different from compute units (CU)?**  
LYFTIUM bills and limits by requests per minute. You do not convert each JSON-RPC method into compute units or credits to estimate cost.

**What does HTTP 503 mean?**  
The sync gate is closed (fail-closed). Do not treat the response as a valid tip — retry, fail over, and check [Status](https://app.lyftium.com/status).

**Do you support L2s or testnets?**  
No. Ethereum Mainnet only.

**Is there a free tier?**  
No. Plans start at Standard ($29/mo, 100 rpm).

**Can I put the API key in the URL?**  
No. Authentication is `X-Api-Key` header only. Query-string and path keys are not supported.

## Scope

- **Supported:** Ethereum Mainnet.
- **Not supported:** other chains, L2s, or testnets.

## Support

Manage keys, plans, and billing at [https://app.lyftium.com](https://app.lyftium.com).

---

MIT © [LYFTIUM](https://www.lyftium.com) · [Portal](https://app.lyftium.com) · [Status](https://app.lyftium.com/status) · [Endpoint](https://eth-mainnet-rpc.lyftium.com)
