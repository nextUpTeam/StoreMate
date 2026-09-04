# Idempotency

Commands that can be retried must accept an idempotency key, especially sale creation, payments, stock receipts, and offline synchronization.

The server should store the key with the operation result and the authenticated store/user context. A repeated request with the same key returns the original result; reusing a key with different payload data is rejected.

Idempotency records and the business write must be committed in the same transaction where possible. Use database uniqueness constraints to protect against concurrent duplicate requests. Reads generally do not need idempotency keys.
