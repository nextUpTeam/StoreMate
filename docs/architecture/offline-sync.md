# Offline Synchronization

Offline mode is a planned capability, not a prerequisite for the first online release.

When required, the mobile client should queue explicit commands such as `CreateSale`, `ReceiveStock`, and `AdjustInventory`. Each command must include an identifier, device/user context, creation time, and a retry state.

The API should:

- process commands idempotently;
- validate current stock and permissions on the server;
- record accepted, rejected, and conflict outcomes;
- use server timestamps and authoritative inventory state;
- expose synchronization status without exposing database details.

Do not silently merge conflicting inventory changes. Define a business resolution rule before enabling offline sales in production.
