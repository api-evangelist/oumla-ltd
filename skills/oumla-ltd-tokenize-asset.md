---
name: Tokenize an asset (collection, issue, mint)
description: Create a tokenization collection on Oumla, issue a token, mint supply, and track the async workflow.
api: openapi/oumla-ltd-openapi-original.json
operations: [createCollection, issueNewToken, mintToken, getWorkflowStatus, getCollectionById, getTokens]
---

# Tokenize an asset

Issue and mint tokens (e.g. tokenized securities, fund units, sukuk, or NFTs) within a collection.

## Auth
Send the `x-api-key` header. Test on `https://sandbox.oumla.com` against `tETH` (Sepolia) before mainnet.

## Steps
1. `createCollection` (`POST /api/v1/tokenization/collections`) — starts the create-collection workflow; returns a `workflowId`. The collection is created asynchronously.
2. `getWorkflowStatus` (`GET /api/v1/workflows/{workflowId}/status`) — poll until the collection workflow completes; then confirm with `getCollectionById` (`GET /api/v1/tokenization/collections/{id}`).
3. `issueNewToken` (`POST /api/v1/tokenization/tokens`) — issue a token; returns `workflowId`, `tokenId`, and status. Poll `getWorkflowStatus`.
4. `mintToken` (`POST /api/v1/tokenization/collections/{id}/tokens/mint`) — start the mint workflow (requires mint policy). Poll `getWorkflowStatus`.
5. `getTokens` (`GET /api/v1/tokenization/tokens`) — list issued tokens (offset pagination).

## Notes
- All mutations are async workflows — never assume immediate completion; always poll `getWorkflowStatus`.
- There is no idempotency key; the workflow model prevents duplicate side effects. Do not blindly retry a mutation — re-poll the existing `workflowId`.
- `burnToken` (`POST /api/v1/tokenization/collections/{id}/tokens/burn`) removes supply; requires burn policy.
- Collection not found -> `ERR_068`; failed token issue -> `ERR_186`.
