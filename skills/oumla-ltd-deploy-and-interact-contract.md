---
name: Deploy and interact with a smart contract
description: Create a contract template, deploy it to a network, then call read/write functions and fetch the receipt on Oumla.
api: openapi/oumla-ltd-openapi-original.json
operations: [createContract, deployContract, getWorkflowStatus, readCallFunction, writeCallFunction, getTransactionReceipt, getDeployedContractAbi]
---

# Deploy and interact with a smart contract

Deploy a smart contract from a reusable template and call its functions.

## Auth
Send the `x-api-key` header. Use `https://sandbox.oumla.com` with a testnet `networkId`.

## Steps
1. `createContract` (`POST /api/v1/contract-templates`) — register a template (ABI, bytecode, metadata). Returns a `templateId`.
2. (optional) `getContractConstructorById` (`GET /api/v1/contract-templates/{templateId}/getConstructorInfo`) — inspect constructor params before deploying.
3. `deployContract` (`POST /api/v1/contract-templates/{templateId}/deploy`) — deploy to a `networkId`; returns a `workflowId`.
4. `getWorkflowStatus` (`GET /api/v1/workflows/{workflowId}/status`) — poll until deployment completes. Deployment failure -> `ERR_126`; in-progress -> `ERR_146`.
5. `getDeployedContractAbi` (`GET /api/v1/contract-interactions/network/{networkId}/contract-address/{contractAddress}/functions`) — list callable functions.
6. Interact:
   - `readCallFunction` (`POST .../functions/read`) — gas-free read call.
   - `writeCallFunction` (`POST .../functions/write`) — starts a write transaction workflow (requires interact-contract policy).
7. `getTransactionReceipt` (`GET /api/v1/contract-interactions/network/{networkId}/tx/{txId}`) — fetch the receipt for the write.

## Notes
- Function not found in ABI -> `ERR_076`; invalid contract address -> `ERR_176`.
- Write calls and deploys are async — poll workflow/receipt rather than retrying.
