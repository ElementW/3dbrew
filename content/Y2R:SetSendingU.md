+++
title = 'Y2R:SetSendingU'
+++

# Request

| Index Word | Description                                |
|------------|--------------------------------------------|
| 0          | Header code \[0x00110102\]                 |
| 1          | Source U-component Buffer                  |
| 2          | Buffer Size                                |
| 3          | s16, Transfer Unit                         |
| 4          | s16, Transfer Stride                       |
| 5          | 0x0                                        |
| 6          | Source Process Handle (usually 0xFFFF8001) |

# Response

| Index Word | Description |
|------------|-------------|
| 0          | Header code |
| 1          | Result code |
