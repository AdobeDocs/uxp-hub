---
title: require('uxp').storage.secureStorage
description: SecureStorage provides a protected storage which can be used to store sensitive data
---

# require('uxp').storage.secureStorage
SecureStorage provides a protected storage which can be used to store sensitive data
per plugin. SecureStorage takes a key-value pair and encrypts the value before being
stored. After encryption, it stores the key and the encrypted value pair. When the value
is requested with an associated key, it's retrieved after being decrypted. Please note
that the key is not encrypted thus it's not protected by the cryptographic operation.

Caveats for SecureStorage are as follows:
1. SecureStorage is not an appropriate storage for sensitive data which wants to keep
secret from the current user. SecureStorage is protected under the current user's
account credential. It means the encrypted data can be at risk of being decrypted
with the current user's privilege.
2. Data in SecureStorage can be lost for various reasons. For an example, the user
could uninstall the host application and delete the secure storage. Or, the cryptographic
information used by the secure storage could be damaged by the user accidentally, and
it will result in loss of data without the secure storage being removed. SecureStorage
should be regarded as a cache rather than a persistent storage. Data in SecureStorage
should be able to be regenerated from plugins after the time of loss.

**Note:** Since UXP v9.0, SecureStorage methods follow the Web Storage API specification for error handling. The return and error behavior documented below reflects this change; older runtimes may behave differently.



## length : `number`
**Read only**
Number of items stored in the secure storage.



## setItem(key, value)
Stores a key and value pair after the value is encrypted in a secure storage.

**Returns**: `Promise<void>` - Promise that resolves when the value is stored

**Throws**:

- `TypeError` If either key or value doesn't have one of acceptable types
- `DOMException` If the value cannot be stored (e.g., QuotaExceededError)


| Param | Type | Description |
| --- | --- | --- |
| key | `string` | Key to set value |
| value | `string` \| `ArrayBuffer` \| `TypedArray` | Value for a key. |



## getItem(key)
Retrieves a value associated with a provided key after the value is being decrypted from a secure storage.

**Returns**: `Promise<Uint8Array|null>` Promise that resolves with a Uint8Array, or null if the key doesn't exist

**Throws**:

- `TypeError` If key is not a string


| Param | Type | Description |
| --- | --- | --- |
| key | `string` | Key to get value |



## removeItem(key)
Removes a value associated with a provided key.
If the key doesn't exist or operation fails, the method silently completes.

**Returns**: `Promise<void>` - Promise that resolves when the operation completes

**Throws**:

- `TypeError` If key is not a string


| Param | Type | Description |
| --- | --- | --- |
| key | `string` | Key to remove value |



## key(index)
Returns a key which is stored at the given index.

**Returns**: `number` - Key which is stored at the given index.  

| Param | Type | Description |
| --- | --- | --- |
| index | `number` | Integer representing the number of the key |



## clear()
Clear all values in a secure storage.
If the storage is empty or clear operation fails, the method silently completes.

**Returns**: `Promise<void>` - Promise that resolves when the operation completes

  