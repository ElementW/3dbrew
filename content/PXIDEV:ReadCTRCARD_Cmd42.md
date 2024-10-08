+++
title = 'PXIDEV:ReadCTRCARD Cmd42'
+++

# Request

| Index Word | Description                                                   |
|------------|---------------------------------------------------------------|
| 0          | Header code \[0x00030102\]                                    |
| 1          | Buffer Size                                                   |
| 2          | Sector Offset                                                 |
| 3          | Sector Count                                                  |
| 4          | u8, [SectorSize](Gamecard_Services_PXI#sectorsize "wikilink") |
| 5          | (Size \<\< 8) \| 0x4                                          |
| 6          | Buffer Pointer                                                |

# Response

| Index Word | Description |
|------------|-------------|
| 0          | Header code |
| 1          | Result code |

# Description

Reads from CTRCARD at an offset. The 32-bit words of the command written
to the [CTRCARD_CMD](CTRCARD_Registers#ctrcard_cmd "wikilink") register
are "0x42000000 \| (SectorOffset \>\> 23), SectorOffset \<\< 9, 0, 0"
