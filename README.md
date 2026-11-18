# audits

Public security disclosures and audit reports by [`schutzevm`](https://github.com/schutzevm).

## Disclosures

| Target | Stack | Finding | Severity | Advisory | Fix | Writeup |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `paycrest/contracts` | EVM / Solidity | Permissionless `settleIn()` can pre-occupy deterministic `createOrder()` IDs (per-address DoS) | High | [GHSA-vxc4-x835-m4h5](https://github.com/paycrest/contracts/security/advisories/GHSA-vxc4-x835-m4h5) | [`e75a8e6`](https://github.com/paycrest/contracts/commit/e75a8e67926069ca3d51bc295c74f27f627946d2) | [report](./disclosures/paycrest/GHSA-vxc4-x835-m4h5.md) |
