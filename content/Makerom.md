+++
title = 'Makerom'
categories = ["PC utilities"]
+++

makerom is a tool which can be used to create [NCCH](NCCH "wikilink"),
[CCI](NCSD "wikilink"), and [CIA](CIA "wikilink") files.

## Format Overviews

### NCCH

The native format storing code binaries and data archives for the 3DS is
[NCCH](NCCH "wikilink"). [NCCH](NCCH "wikilink") files are comprised of:

- code/exheader/plainregion (used for code execution) (plainregion just
  lists included SDK library add-ons)
- icon (app title text, icon, homemenu settings, see
  [here](SMDH "wikilink")
- banner (cbmd + cwav, i.e. the upper screen banner/sound shown on the
  homemenu)
- logo (the splash screen displayed after an application is launched
  from the homemenu)
- romfs (read-only filesystem used to store resources)

Typical uses for NCCH files include:

- Executable image (code+exheader+icon+banner+logo+romfs)
- e-Manual archive (accessed from homemenu) (romfs)
- [DLP](Download_Play "wikilink") child CIA archive (accessed from
  application) (romfs)
- Update Data archive (romfs)
- Standalone data archive (romfs)
- DLC index archive (icon+romfs)
- DLC archive (romfs)

### CCI

The native format for gamecard images is [CCI](NCSD "wikilink") and is a
NCCH container format. CCI files are limited to containing 8 NCCH files,
and can contain NCCH files for applications titles only.

#### NCCH configuration for CCI

| NCCH                  | Required | Index |
|-----------------------|----------|-------|
| Executable image      | YES      | 0     |
| e-Manual archive      | NO       | 1     |
| DLP child CIA archive | NO       | 2     |
| Update Data archive   | NO       | 7     |

### CIA

The native format for packaging NCCH files for install is
[CIA](CIA "wikilink"), which is also a NCCH container format. CIA files
are limited to containing 65535 NCCH files and can be used to contain
NCCH files for any title type. CIA files also contain **signed** data
used by the 3DS for general title management and DRM. Installing custom
CIA files on a 3DS which also uses eShop/SysUpdates is unwise as
conflicts will likely occur.

#### NCCH configurations for CIA

Applications (Application/DlpChild/Demo/Patch/SystemApplication):

| NCCH                  | Required | Index |
|-----------------------|----------|-------|
| Executable image      | YES      | 0     |
| e-Manual archive      | NO       | 1     |
| DLP child CIA archive | NO       | 2     |

System Applet/Module:

| NCCH             | Required | Index |
|------------------|----------|-------|
| Executable image | YES      | 0     |

System Data Archives:

| NCCH         | Required | Index |
|--------------|----------|-------|
| Data archive | YES      | 0     |

DLC:

The number of DLC data archives in DLC varies for each DLC.

| NCCH              | Required | Index  |
|-------------------|----------|--------|
| DLC index archive | YES      | 0      |
| DLC data archive  | YES      | Varies |

## Using Makerom

### Command line

```
makerom [general args] [rsf args] [crypto args] [ncch 0 build args] [cci args] [cia args]
```

**General Arguments**

| Argument    | Acceptable values              | Notes                                                                                                               |
|-------------|--------------------------------|---------------------------------------------------------------------------------------------------------------------|
| -f <format> | 'ncch'/'cxi'/'cfa'/'cci'/'cia' | Specify the output file format. 'ncch'/'cxi'/'cfa' has no affect, probably parsed without error for legacy support. |
| -o <path>   | Valid file path.               | Specify name/path for output file. Makerom will decided a name if this is not specified.                            |
| -v          | not required                   | Enables verbose output.                                                                                             |

**RSF Arguments**

| Argument         | Acceptable values | Notes                                                                                                                 |
|------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------|
| -rsf <path>      | Valid file path   | Specify the path to Rom Specification File(RSF). See below for creating RSF.                                          |
| -D<NAME>=<VALUE> |                   | This is used to substitute where "\$(<NAME>)" exists in the RSF files with "<VALUE>". (Uppercase isn't a requirement) |

**Crypto Arguments**

| Argument         | Acceptable values                    | Notes                                                                                                                                                                                                                       |
|------------------|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -target <target> | 't'/'d'/'p'                          | Specify key-chain. This affects encryption, signing and '-desc' template availability. t=test, suitable for homebrew. d=devkit(incomplete), suitable for devkits. p=retail(unusable), suitable for signing retail software? |
| -ckeyid <index>  | Any value between 0-255 (inclusive). | Overrides the default common key used to encrypt CIA title keys.                                                                                                                                                            |
| -showkeys        | none                                 | Dumps loaded key-chain to stdout.                                                                                                                                                                                           |

**NCCH Build Arguments**

| Argument             | Acceptable values                                                       | Notes                                                                                                                                                                                                                                                                                                |
|----------------------|-------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -elf <file>          | Valid file path                                                         | Specify ELF. See below for creating ELF.                                                                                                                                                                                                                                                             |
| -icon <file>         | Valid file path                                                         | Specify [icon](SMDH "wikilink").                                                                                                                                                                                                                                                                     |
| -banner <file>       | Valid file path                                                         | Specify banner.                                                                                                                                                                                                                                                                                      |
| -desc <apptype>:<fw> | <apptype>='app'/'ecapp'/'demo'/'dlpchild'. <fw>='kernel version minor'. | Use a template for [exheader/accessdesc](Exheader "wikilink"). These are hard-coded, so not all firmwares have a template. A value from 1-7 can be used in place of 'kernel version minor'. A template shouldn't be used if the title needs "special" permissions, the RSF must be configured fully. |
| -exefslogo           | none                                                                    | Include logo in ExeFS. Required for usage on \<5.0 systems.                                                                                                                                                                                                                                          |

Arguments useful for rebuilding a NCCH file:

| Argument         | Acceptable values | Notes                                             |
|------------------|-------------------|---------------------------------------------------|
| -code <file>     | Valid file path   | Specify decompressed/plaintext exefs code binary. |
| -exheader <file> | Valid file path   | Specify plaintext exheader binary.                |
| -logo <file>     | Valid file path   | Specify logo.                                     |
| -plainrgn <file> | Valid file path   | Specify NCCH plain-region.                        |
| -romfs <file>    | Valid file path   | Specify an unencrypted RomFS binary.              |

**CCI Arguments**

| Argument                | Acceptable values                                                 | Notes                                                                                   |
|-------------------------|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| -content <path>:<index> | <path>=Valid file path. <index>=Any value between 0-7 (inclusive) | Include a built NCCH file in the CCI container. "-i" can be used instead of "-content". |
| -devcci                 | none                                                              | Build a debug CCI?                                                                      |
| -nomodtid               | none                                                              | Don't modify the TitleIds of NCCH files included to match NCCH0                         |
| -alignwr                | none                                                              | Align the offset for the Card2 writable region to the end of the last NCCH in the CCI.  |

**CIA Arguments**

| Argument                     | Acceptable values                                                                                                           | Notes                                                                                                                                           |
|------------------------------|-----------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| -content <path>:<index>:<id> | <path>=Valid file path. <index>=Any value between 0x0-0xFFFF (inclusive). <id>=Any value between 0x0-0xFFFFFFFF (inclusive) | Include a built NCCH file in the CIA container. If <id> isn't specified, it will be generated randomly. "-i" can be used instead of "-content". |
| -major <version>             | Any value between 0-63 (inclusive)                                                                                          | Specify the version major for the title. This cannot be used with "-dver".                                                                      |
| -minor <version>             | Any value between 0-63 (inclusive)                                                                                          | Specify the version minor for the title. This cannot be used with "-dver".                                                                      |
| -micro <version>             | Any value between 0-15 (inclusive)                                                                                          | Specify the version micro for the title.                                                                                                        |
| -dver <version>              | Any value between 0-4095 (inclusive)                                                                                        | Specify the data-title version for the title. This cannot be used with "-major" or "-minor".                                                    |
| -dlc                         | none                                                                                                                        | Specify this flag when building a DLC CIA.                                                                                                      |
| -rand                        | none                                                                                                                        | Use a random title key to encrypt CIA content.                                                                                                  |

#### Examples

General examples:

**Create CXI**

```
makerom -o sample.cxi -rsf sample.rsf -target t -elf sample.elf -icon sample.icn -banner sample.bnr -desc app:4
```

**Create CFA**

```
makerom -o sample.cfa -rsf sample.rsf -target t
```

**Create CCI**

```
makerom -f cci -o sample.cci -target t -i sample.cxi:0 -i sample.cfa:1
```

**Create CIA**

```
makerom -f cia -o sample.cia -target t -i sample.cxi:0:0 -i sample.cfa:1:1
```

Makerom supports building a NCCH file and including it automatically (as
index 0) into a NCCH container:

**Create CCI and CXI at the same time and include a CFA**

```
makerom -f cci -o sample.cci -rsf sample.rsf -target t -elf sample.elf -icon sample.icn -banner sample.bnr -desc app:4 -i sample.cfa:1
```

**Create CIA and CXI at the same time and include a CFA**

```
makerom -f cia -o sample.cia -rsf sample.rsf -target t -elf sample.elf -icon sample.icn -banner sample.bnr -desc app:4 -i sample.cfa:1:1
```

Rebuilding CXI:

```
makerom -o rebuild.cxi -rsf rebuild.rsf -target t -code rebuild/code.bin -exheader rebuild/exheader.bin -icon rebuild/icon.bin -banner rebuild/banner.bin -romfs rebuild/romfs.bin
```

### Creating RSF files

Inspired by Nintendo's format for their makerom, a yaml configuration
file is required for creating NCCH files. CIA/CCI can be created without
using a RSF file, but default settings will be used.

For CXI, RSF files can be used to specify permissions, and access
control settings. Makerom can use default settings by use of the "-desc"
option, which removes the requirement for specifying them in the RSF
file.

Sample RSF to be used with "-desc":
[download](https://gist.githubusercontent.com/3DSGuy/83e12e0ae3dcccb9827f/raw/sample0.rsf)
(link broken)

Sample RSF to be used without "-desc":
[download](https://gist.github.com/jakcron/9f9f02ffd94d98a72632)

### Creating ELF files

The latest devkitARM used in conjunction with
[ctrulib](https://github.com/smealum/ctrulib) can create ELF files
compatible with makerom.

ELF files that are created using the official SDK are also supported by
makerom.

## Compiling Source

For Windows a MinGW-W64/MSYS build setup is required.

For Linux, gcc/g++/make must be installed.

All additional libraries used by makerom (polarssl/libyaml) are included
in the source, and are linked statically.
