---
name: Withdraw funds and track the workflow
description: Initiate a withdrawal from an Oumla address and monitor transactions to completion.
api: openapi/oumla-ltd-openapi-original.json
operations: [getNativeBalance, createWithdraw, getWorkflowStatus, getTransactionsByAddress]
---

# Withdraw funds and track the workflow

Move BTC/tBTC or ETH/tETH out of an Oumla-managed address.

## Auth
Send the `x-api-key` header. Test on `https://sandbox.oumla.com` with testnet funds from a public faucet.

## Steps
1. `getNativeBalance` (`GET /api/v1/portfolio/native-balance`) — confirm the address holds enough to cover the amount plus network fees. Insufficient funds -> `ERR_236`; no UTXOs (Bitcoin) -> `ERR_226`.
2. `createWithdraw` (`POST /api/v1/withdraw`) — initiate the withdrawal (supports BTC, tBTC, ETH, tETH). Returns pending status.
3. `getWorkflowStatus` (`GET /api/v1/workflows/{workflowId}/status`) — poll if a `workflowId` is returned.
4. `getTransactionsByAddress` (`GET /api/v1/transactions/address/{address}`) — verify the outbound transaction is broadcast/confirmed (offset pagination).

## Notes
- Account for network fees separately from the withdrawal amount.
- No idempotency key — do not resubmit `createWithdraw` on timeout; check `getTransactionsByAddress` first to avoid a double-spend.
- Errors follow the ERR_* envelope; include the `requestId` when contacting support@oumla.com.
