+++
title = 'FS:IsSdmcWritable'
+++

# Request

| Index Word | Description                |
|------------|----------------------------|
| 0          | Header code \[0x08180000\] |

# Response

| Index Word | Description                |
|------------|----------------------------|
| 0          | Header code                |
| 1          | Result code                |
| 2          | u8, Whether SD is writable |
