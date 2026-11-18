# `paycrest/contracts` — `GHSA-vxc4-x835-m4h5`

- **Severity:** High
- **Target:** `contracts/Gateway.sol`
- **Advisory:** [GHSA-vxc4-x835-m4h5](https://github.com/paycrest/contracts/security/advisories/GHSA-vxc4-x835-m4h5)
- **Fix Commit:** [`e75a8e6`](https://github.com/paycrest/contracts/commit/e75a8e67926069ca3d51bc295c74f27f627946d2)

## Summary

`Gateway.createOrder()` derives deterministic order IDs from `keccak256(abi.encode(msg.sender, _nonce[msg.sender], block.chainid))` and reverts with `'OrderAlreadyExists'` if `order[orderId].sender != address(0)`.

Because `Gateway.settleIn()` was permissionless (`external whenNotPaused`), accepted an arbitrary caller-supplied `bytes32 _orderId`, and wrote directly into the same `order` mapping (`order[_orderId].sender = _recipient`), any caller could pre-occupy a target address's next deterministic `orderId` at zero net token cost (`_amount = 1` base unit returned to the caller) and permanently block that address from calling `createOrder()` on that chain until the proxy was upgraded.

## Root Cause

1. `createOrder()` incremented `_nonce[msg.sender]`, computed `keccak256(abi.encode(msg.sender, _nonce[msg.sender], block.chainid))`, and enforced `require(order[orderId].sender == address(0), 'OrderAlreadyExists')`.
2. `settleIn()` accepted an arbitrary `_orderId` and `_amount >= 1`, writing `order[_orderId].sender = _recipient` (`_recipient != address(0)`).
3. On `'OrderAlreadyExists'`, `createOrder()` reverted with `_nonce[sender]` and the existing `order` record unchanged, leaving the target address's next order ID permanently blocked.

## Remediation

Domain-separate `settleIn()` storage keys inside the existing `order` mapping (`keccak256(abi.encode('SETTLE_IN', _orderId))`) to preserve the `TransparentUpgradeableProxy` storage layout while preventing `settleIn()` from colliding with `createOrder()` order IDs, fixed in [`e75a8e6`](https://github.com/paycrest/contracts/commit/e75a8e67926069ca3d51bc295c74f27f627946d2).
