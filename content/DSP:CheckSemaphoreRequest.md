+++
title = 'DSP:CheckSemaphoreRequest'
+++

# Request

| Index Word | Description                |
|------------|----------------------------|
| 0          | Header code \[0x000B0000\] |

# Response

| Index Word | Description                                 |
|------------|---------------------------------------------|
| 0          | Header code                                 |
| 1          | Result code                                 |
| 2          | u8, 0 = not requested, non-zero = requested |
