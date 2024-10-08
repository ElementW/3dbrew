+++
title = 'BCSTM'
categories = ["File formats"]
+++

This document is about the format of CTR Streams (CSTM).

### Overview

The structure is similar to that of a [BCWAV](BCWAV "wikilink"), with a
few differences, such as its different INFO block format, the addition
of a SEEK block, and the organization of the DATA block samples into
blocks.

These files are either found in rom:\sound\stream\\ or they can be
inside of a CSAR.

### Header

| OFFSET | SIZE | DESCRIPTION                                                                                                            |
|--------|------|------------------------------------------------------------------------------------------------------------------------|
| 0x000  | 4    | Magic (CSTM)                                                                                                           |
| 0x004  | 2    | Endianness (0xFEFF = little, 0xFFFE = big)                                                                             |
| 0x006  | 2    | Header Size (0x40 due to [Info Block](#info_block "wikilink") alignment)                                               |
| 0x008  | 4    | Version (0x02000000)                                                                                                   |
| 0x00C  | 4    | File Size                                                                                                              |
| 0x010  | 2    | Number of Blocks (3)                                                                                                   |
| 0x012  | 2    | Reserved                                                                                                               |
| 0x014  | 12   | [Info Block](#info_block "wikilink") [Sized Reference](#sized_reference "wikilink") (Offset relative to start of file) |
| 0x020  | 12   | [Seek Block](#seek_block "wikilink") [Sized Reference](#sized_reference "wikilink") (Offset relative to start of file) |
| 0x02C  | 12   | [Data Block](#data_block "wikilink") [Sized Reference](#sized_reference "wikilink") (Offset relative to start of file) |

### Block Header

| OFFSET | SIZE | DESCRIPTION |
|--------|------|-------------|
| 0x000  | 4    | Magic       |
| 0x004  | 4    | Size        |

#### Block Types

| MAGIC | TYPE                                 |
|-------|--------------------------------------|
| INFO  | [Info Block](#info_block "wikilink") |
| SEEK  | [Seek Block](#seek_block "wikilink") |
| DATA  | [Data Block](#data_block "wikilink") |

### Info Block

| OFFSET | SIZE | DESCRIPTION                                                                                                                                                                                                                     |
|--------|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 0x000  | 8    | [Block Header](#block_header "wikilink")                                                                                                                                                                                        |
| 0x008  | 8    | [Stream Info](#stream_info "wikilink") [Reference](#reference "wikilink") (Offset relative to this field)                                                                                                                       |
| 0x010  | 8    | [Track Info](#track_info "wikilink") [Reference Table](#reference_table "wikilink") [Reference](#reference "wikilink") (Offset relative to [Stream Info](#stream_info "wikilink") [Reference](#reference "wikilink") field)     |
| 0x018  | 8    | [Channel Info](#channel_info "wikilink") [Reference Table](#reference_table "wikilink") [Reference](#reference "wikilink") (Offset relative to [Stream Info](#stream_info "wikilink") [Reference](#reference "wikilink") field) |
| 0x020  | 56   | [Stream Info](#stream_info "wikilink")                                                                                                                                                                                          |
| 0x058  | X    | [Track Info](#track_info "wikilink") [Reference Table](#reference_table "wikilink")                                                                                                                                             |
| X      | X    | [Channel Info](#channel_info "wikilink") [Reference Table](#reference_table "wikilink")                                                                                                                                         |
| X      | X    | [Track Info](#track_info "wikilink") Entries                                                                                                                                                                                    |
| X      | X    | [Channel Info](#channel_info "wikilink") Entries                                                                                                                                                                                |

If encoding is DSP ADPCM:

| OFFSET | SIZE | DESCRIPTION                                          |
|--------|------|------------------------------------------------------|
| X      | X    | [DSP ADPCM Info](#dsp_adpcm_info "wikilink") Entries |

If encoding is IMA ADPCM:

| OFFSET | SIZE | DESCRIPTION                                          |
|--------|------|------------------------------------------------------|
| X      | X    | [IMA ADPCM Info](#ima_adpcm_info "wikilink") Entries |

The info block is aligned to 0x20 bytes.

#### Encoding

| VALUE | DESCRIPTION |
|-------|-------------|
| 0     | PCM8        |
| 1     | PCM16       |
| 2     | DSP ADPCM   |
| 3     | IMA ADPCM   |

#### Stream Info

| OFFSET | SIZE | DESCRIPTION                                                                                                         |
|--------|------|---------------------------------------------------------------------------------------------------------------------|
| 0x000  | 1    | [Encoding](#encoding "wikilink")                                                                                    |
| 0x001  | 1    | Loop (0 = don't loop, 1 = loop)                                                                                     |
| 0x002  | 1    | Channel Count                                                                                                       |
| 0x003  | 1    | Padding                                                                                                             |
| 0x004  | 4    | Sample Rate                                                                                                         |
| 0x008  | 4    | Loop Start Frame                                                                                                    |
| 0x00C  | 4    | Loop End Frame                                                                                                      |
| 0x010  | 4    | Sample Block Count                                                                                                  |
| 0x014  | 4    | Sample Block Size                                                                                                   |
| 0x018  | 4    | Sample Block Sample Count                                                                                           |
| 0x01C  | 4    | Last Sample Block Size                                                                                              |
| 0x020  | 4    | Last Sample Block Sample Count                                                                                      |
| 0x024  | 4    | Last Sample Block Padded Size                                                                                       |
| 0x028  | 4    | Seek Data Size                                                                                                      |
| 0x02C  | 4    | Seek Interval Sample Count                                                                                          |
| 0x030  | 8    | Sample Data [Reference](#reference "wikilink") (Offset relative to [Data Block](#data_block "wikilink") Data field) |

#### Track Info

| OFFSET | SIZE | DESCRIPTION                                                                                                             |
|--------|------|-------------------------------------------------------------------------------------------------------------------------|
| 0x000  | 1    | Volume                                                                                                                  |
| 0x001  | 1    | Pan                                                                                                                     |
| 0x002  | 2    | Padding                                                                                                                 |
| 0x004  | 8    | Channel Index [Byte Table](#byte_table "wikilink") [Reference](#reference "wikilink") (Offset relative to Volume field) |
| 0x00C  | X    | Channel Index [Byte Table](#byte_table "wikilink") (Padded to 4 bytes)                                                  |

##### Byte Table

| OFFSET | SIZE  | DESCRIPTION |
|--------|-------|-------------|
| 0x000  | 4     | Count       |
| 0x004  | Count | Elements    |

#### Channel Info

| OFFSET | SIZE | DESCRIPTION                                                                   |
|--------|------|-------------------------------------------------------------------------------|
| 0x000  | 8    | ADPCM Info [Reference](#reference "wikilink") (Offset relative to this field) |

##### DSP ADPCM Info

| OFFSET | SIZE | DESCRIPTION                                   |
|--------|------|-----------------------------------------------|
| 0x000  | 32   | [Param](#dsp_adpcm_param "wikilink")          |
| 0x020  | 6    | [Context](#dsp_adpcm_context "wikilink")      |
| 0x026  | 6    | Loop [Context](#dsp_adpcm_context "wikilink") |
| 0x02C  | 2    | Padding                                       |

###### DSP ADPCM Param

| OFFSET | SIZE | DESCRIPTION         |
|--------|------|---------------------|
| 0x000  | 32   | 16-bit Coefficients |

###### DSP ADPCM Context

| OFFSET | SIZE | DESCRIPTION                   |
|--------|------|-------------------------------|
| 0x000  | 1    | 4-bit Predictor + 4-bit Scale |
| 0x001  | 1    | Reserved                      |
| 0x002  | 2    | Previous Sample               |
| 0x004  | 2    | Second Previous Sample        |

##### IMA ADPCM Info

| OFFSET | SIZE | DESCRIPTION                                   |
|--------|------|-----------------------------------------------|
| 0x000  | 4    | [Context](#ima_adpcm_context "wikilink")      |
| 0x004  | 4    | Loop [Context](#ima_adpcm_context "wikilink") |

###### IMA ADPCM Context

| OFFSET | SIZE | DESCRIPTION |
|--------|------|-------------|
| 0x000  | 2    | Data        |
| 0x002  | 1    | Table Index |
| 0x003  | 1    | Padding     |

### Seek Block

| OFFSET | SIZE                                                    | DESCRIPTION                              |
|--------|---------------------------------------------------------|------------------------------------------|
| 0x000  | 8                                                       | [Block Header](#block_header "wikilink") |
| 0x008  | [Block Header](#block_header "wikilink") Size Value - 8 | Data                                     |

The seek block is aligned to 0x20 bytes.

### Data Block

| OFFSET | SIZE                                                    | DESCRIPTION                              |
|--------|---------------------------------------------------------|------------------------------------------|
| 0x000  | 8                                                       | [Block Header](#block_header "wikilink") |
| 0x008  | [Block Header](#block_header "wikilink") Size Value - 8 | Data                                     |

The data block is aligned to 0x20 bytes, as well as the data field's
actual sample data.

### Reference Table

| OFFSET | SIZE       | DESCRIPTION                                                           |
|--------|------------|-----------------------------------------------------------------------|
| 0x000  | 4          | Count                                                                 |
| 0x004  | Count \* 8 | [References](#reference "wikilink") (Offsets relative to Count field) |

### Sized Reference

| OFFSET | SIZE | DESCRIPTION                        |
|--------|------|------------------------------------|
| 0x000  | 8    | [Reference](#reference "wikilink") |
| 0x008  | 4    | Size                               |

### Reference

| OFFSET | SIZE | DESCRIPTION                  |
|--------|------|------------------------------|
| 0x000  | 2    | Type ID                      |
| 0x002  | 2    | Padding                      |
| 0x004  | 4    | Offset ("null" = 0xFFFFFFFF) |

#### Reference Types

| ID     | TYPE                                           |
|--------|------------------------------------------------|
| 0x0100 | [Byte Table](#byte_table "wikilink")           |
| 0x0101 | [Reference Table](#reference_table "wikilink") |
| 0x0300 | [DSP ADPCM Info](#dsp_adpcm_info "wikilink")   |
| 0x0301 | [IMA ADPCM Info](#ima_adpcm_info "wikilink")   |
| 0x1F00 | [Sample Data](#data_block "wikilink")          |
| 0x4000 | [Info Block](#info_block "wikilink")           |
| 0x4001 | [Seek Block](#seek_block "wikilink")           |
| 0x4002 | [Data Block](#data_block "wikilink")           |
| 0x4100 | [Stream Info](#stream_info "wikilink")         |
| 0x4101 | [Track Info](#track_info "wikilink")           |
| 0x4102 | [Channel Info](#channel_info "wikilink")       |

## Tools

The following tools can play BCSTMs and convert them to other formats:

- [Isabelle Sound Editor](https://gota7.github.io/Citric-Composer/)
- vgmstream
- Every File Explorer

[Category:File formats](Category:File_formats "wikilink")
