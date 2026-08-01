---
name: Create a wallet and receive address
description: Set up a profile, a wallet, and a blockchain receive address on Oumla, then check its balance.
api: openapi/oumla-ltd-openapi-original.json
operations: [createProfile, createWallet, createAddress, createAddressV2, getNativeBalance]
---

# Create a wallet and receive address

Provision a receiving address for a user or merchant on a supported network.

## Auth
Send the `x-api-key` header on every request (generate the key from the dashboard). Use a sandbox key against `https://sandbox.oumla.com` with testnets `tBTC` or `tETH` (Sepolia).

## Steps
1. `createProfile` (`POST /api/v1/profiles`) — create a profile (user/department/merchant) with a unique `reference`. Duplicate references return `ERR_001`.
2. Fetch the target `networkId` — call `getEnabledNetworks` (`GET /api/v1/network/enabled`) or `getNetworks` (`GET /api/v1/network/organization`).
3. Create the address:
   - Preferred: `createAddressV2` (`POST /api/v2/addresses/generate`) — auto-creates the wallet if none exists, then generates the address for the given `networkId` and `reference`.
   - Or two-step: `createWallet` (`POST /api/v1/wallets/generate`) then `createAddress` (`POST /api/v1/addresses/generate`).
4. `getNativeBalance` (`GET /api/v1/portfolio/native-balance`) — read the native (BTC/ETH) balance for the new address.

## Notes
- Offset pagination on list endpoints: `skip`/`take` (max 100); bad values return `ERR_022`.
- Mainnet (BTC/ETH) requires support approval; testnets are available immediately.
- Errors follow the ERR_* envelope — see errors/oumla-ltd-error-codes.yml; include `requestId` in support tickets.
