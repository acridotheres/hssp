<p align="center">
  <img align="center" src="https://img.shields.io/github/stars/HSSPfile?style=for-the-badge&logo=github&color=%23ff0">
  <img align="center" src="https://img.shields.io/github/all-contributors/HSSPfile/specification?label=Contributors&logo=github&style=for-the-badge">

  <h1 align="center">HSSP</h1>
  <h3 align="center">Huge Size Supporting Package</h3>
</p>

---

Welcome to the HSSP specification!

If you have any feature requests for future versions, please open an issue.

Read this in other languages:

- [Deutsch](LIESMICH.md)

---

## Table of contents

- [0. About this specification](#0-about-this-specification)
- [1. Introduction](#1-introduction)
- [2. Versions](#2-versions)
  - [2.1. HSSP v1 (Wrongfolder/WFLD)](#21-hssp-v1-wrongfolderwfld)
    - [2.1.1. Features](#211-features)
    - [2.1.2. File structure](#212-file-structure)
      - [2.1.2.1. Header](#2121-header)
      - [2.1.2.2. Body](#2122-body)
      - [2.1.2.3. Encryption](#2123-encryption)
  - [2.2. HSSP v2 (Rightfolder/RFLD)](#22-hssp-v2-rightfolderrfld)
    - [2.2.1. New features](#221-new-features)
    - [2.2.2. File structure](#222-file-structure)
      - [2.2.2.1. Header](#2221-header)
      - [2.2.2.2. Body](#2222-body)
      - [2.2.2.3. Encryption](#2223-encryption)
  - [2.3. HSSP v3 (Doubleheader/DHDR)](#23-hssp-v3-doubleheaderdhdr)
    - [2.3.1. New features](#231-new-features)
    - [2.3.2. File structure](#232-file-structure)
      - [2.3.2.1. Header](#2321-header)
      - [2.3.2.2. Body](#2322-body)
      - [2.3.2.3. Encryption](#2323-encryption)
  - [2.4. HSSP v4 (Indexed/IDXD)](#24-hssp-v4-indexedidxd)
    - [2.4.1. New features](#241-new-features)
    - [2.4.2. Removed features](#242-removed-features)
    - [2.4.3. File structure](#243-file-structure)
      - [2.4.3.1. Header](#2431-header)
      - [2.4.3.2. Index](#2432-index)
      - [2.4.3.2. Body](#2433-body)
- [3. Compression algorithms](#3-compression-algorithms)
- [4. Contributors](#4-contributors)

---

## 0. About this specification

This is the official HSSP specification, licensed under the [MIT License](LICENSE). All dates are in ISO 8601 format (YYYY-MM-DD).

## 1. Introduction

HSSP is designed to support huge file sizes while being way better than other container formats like ZIP, RAR, etc.

## 2. Versions

HSSP has multiple versions, each with different features.

### 2.1. HSSP v1 (Wrongfolder/WFLD)

| Release date |
| :----------: |
|  2023-03-14  |

Wrongfolder is the first version of HSSP. It's called Wrongfolder, because @Le0X8 published a wrong folder of the first HSSP implementation in JavaScript ([&rarr; official JavaScript library](https://github.com/HSSPfile/js)).

#### 2.1.1. Features

- Storing files
- Content encryption
- Integrity check
- "Index file" (you can define a main file of the package)

#### 2.1.2. File structure

The file structure of Wrongfolder is very simple. It consists of a 64 byte [header](#2121-header) and a [body](#2122-body).

##### 2.1.2.1. Header

| Offset | Length | Description                                                            |   Type   |
| :----: | :----: | :--------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `SFA\x00`                                                 |  UTF-8   |
|  0x4   |  0x4   | MurmurHash3 checksum of the [body](#2122-body) (files)                 | Uint32LE |
|  0x8   |  0x4   | Number of contained files                                              | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256) (filled with 0x0 if no password is set) |          |
|  0x2C  |  0x10  | Initialization vector (IV) (filled with 0x0 if no password is set)     |          |
|  0x3C  |  0x4   | "Index file" (main file of the package, e. g. a README)                | Uint32LE |

##### 2.1.2.2. Body

The body consists of multiple files.

A file is structured like this:

|  Offset   | Length | Description                                                                          |   Type   |
| :-------: | :----: | :----------------------------------------------------------------------------------- | :------: |
|    0x0    |  0x8   | File length in Bytes (= FL)                                                          | Uint64LE |
|    0x8    |  0x2   | Name length in Bytes (= NL)                                                          | Uint16LE |
|    0xA    |   NL   | Name of the file (if the file starts with `//`, the file will be parsed as a folder) |  UTF-8   |
|  0xA+NL   |   FL   | File contents (if the file is a folder, this will be ignored.)                       |          |
| 0xA+NL+FL |   NL   | Unused. You can put anything here.                                                   |          |

##### 2.1.2.3. Encryption

The encryption is done with AES-256-CBC. The password is hashed with SHA-256 and the result is hashed again with SHA-256. The result is the key.

When encrypting, you have to encrypt the whole body.

### 2.2. HSSP v2 (Rightfolder/RFLD)

| Release date |
| :----------: |
|  2023-03-14  |

#### 2.2.1. New features

_none_

#### 2.2.2. File structure

The file structure of Rightfolder is very simple. It consists of a 64 byte [header](#2221-header) and a [body](#2222-body).

##### 2.2.2.1. Header

| Offset | Length | Description                                                            |   Type   |
| :----: | :----: | :--------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                    |  UTF-8   |
|  0x4   |  0x4   | MurmurHash3 checksum of the [body](#2222-body) (files)                 | Uint32LE |
|  0x8   |  0x4   | Number of contained files                                              | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256) (filled with 0x0 if no password is set) |          |
|  0x2C  |  0x10  | Initialization vector (IV) (filled with 0x0 if no password is set)     |          |
|  0x3C  |  0x4   | "Index file" (main file of the package, e. g. a README)                | Uint32LE |

##### 2.2.2.2. Body

The body consists of multiple files.

A file is structured like this:

|  Offset   | Length | Description                                                                          |   Type   |
| :-------: | :----: | :----------------------------------------------------------------------------------- | :------: |
|    0x0    |  0x8   | File length in Bytes (= FL)                                                          | Uint64LE |
|    0x8    |  0x2   | Name length in Bytes (= NL)                                                          | Uint16LE |
|    0xA    |   NL   | Name of the file (if the file starts with `//`, the file will be parsed as a folder) |  UTF-8   |
|  0xA+NL   |   FL   | File contents (if the file is a folder, this will be ignored.)                       |          |
| 0xA+NL+FL |   NL   | Unused. You can put anything here.                                                   |          |

##### 2.2.2.3. Encryption

The encryption is done with AES-256-CBC. The password is hashed with SHA-256 and the result is hashed again with SHA-256. The result is the key.

When encrypting, you have to encrypt the whole body.

### 2.3. HSSP v3 (Doubleheader/DHDR)

| Release date |
| :----------: |
|  2023-03-14  |

The header is now 128 bytes long.

#### 2.3.1. New features

_none_

#### 2.3.2. File structure

The file structure of Wrongfolder is very simple. It consists of a 128 byte [header](#2321-header) and a [body](#2322-body).

##### 2.3.2.1. Header

| Offset | Length | Description                                                            |   Type   |
| :----: | :----: | :--------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                    |  UTF-8   |
|  0x4   |  0x4   | MurmurHash3 checksum of the [body](#2322-body) (files)                 | Uint32LE |
|  0x8   |  0x4   | Number of contained files                                              | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256) (filled with 0x0 if no password is set) |          |
|  0x2C  |  0x10  | Initialization vector (IV) (filled with 0x0 if no password is set)     |          |
|  0x3C  |  0x4   | "Index file" (main file of the package, e. g. a README)                | Uint32LE |
|  0x40  |  0x40  | Unused. You can put anything here.                                     |          |

##### 2.3.2.2. Body

The body consists of multiple files.

A file is structured like this:

|  Offset   | Length | Description                                                                          |   Type   |
| :-------: | :----: | :----------------------------------------------------------------------------------- | :------: |
|    0x0    |  0x8   | File length in Bytes (= FL)                                                          | Uint64LE |
|    0x8    |  0x2   | Name length in Bytes (= NL)                                                          | Uint16LE |
|    0xA    |   NL   | Name of the file (if the file starts with `//`, the file will be parsed as a folder) |  UTF-8   |
|  0xA+NL   |   FL   | File contents (if the file is a folder, this will be ignored.)                       |          |
| 0xA+NL+FL |   NL   | Unused. You can put anything here.                                                   |          |

##### 2.3.2.3. Encryption

The encryption is done with AES-256-CBC. The password is hashed with SHA-256 and the result is hashed again with SHA-256. The result is the key.

When encrypting, you have to encrypt the whole body.

### 2.4. HSSP v4 (Indexed/IDXD)

| Release date |
| :----------: |
|  2023-07-11  |

#### 2.4.1. New features

- Indexing
- Compression
- Splitting

#### 2.4.2. Removed features

- "Index file" &rarr; replaced by a "main file" file attribute in the index

#### 2.4.3. File structure

HSSP Indexed is the first "real update" to HSSP. It now really uses the additional 64 bytes of the header and is more complex than the versions before. Make sure to decrypt the file before decompressing it.

##### 2.4.3.1. Header

| Offset | Length | Description                                                                                        |   Type   |
| :----: | :----: | :------------------------------------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                                                |  UTF-8   |
|  0x4   |  0x1   | File standard version (0x4)                                                                        |  Uint8   |
|  0x5   |  0x3   | Unused. You can put anything here.                                                                 |          |
|  0x8   |  0x4   | Number of contained files                                                                          | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256)                                                                     |          |
|  0x2C  |  0x10  | Initialization vector (IV)                                                                         |          |
|  0x3C  |  0x4   | Used [compression algorithm](#3-compression-algorithms)                                            |  UTF-8   |
|  0x40  |  0x4   | Checksum of the [body](#2433-body) (files)                                                         | Uint32LE |
|  0x44  |  0x8   | File count of all the files, even those that are not in this package! (may be 0x0 if not splitted) | Uint64LE |
|  0x4C  |  0x8   | Offset of the file that has been splitted. (0x0 if not splitted)                                   | Uint64LE |
|  0x54  |  0x4   | Checksum of the previous package (0x0 if not splitted or not existing)                             | Uint32LE |
|  0x58  |  0x4   | Checksum of the next package (0x0 if not splitted or not existing)                                 | Uint32LE |
|  0x5C  |  0x4   | ID of this package (0x0 if not splitted) (0 is the first part, 1 is the second, ...)               | Uint32LE |
|  0x60  |  0x10  | Comment                                                                                            |  UTF-8   |
|  0x70  |  0x10  | Generator credits, the official JavaScript library puts `hssp 5.0.0 @ npm` in here.                |  UTF-8   |

##### 2.4.3.2. Index

The index is structured like this:

|          Offset          |   Length    | Description                                                                                                       |   Type   |
| :----------------------: | :---------: | :---------------------------------------------------------------------------------------------------------------- | :------: |
|           0x0            |     0x8     | File length in Bytes                                                                                              | Uint64LE |
|           0x8            |     0x2     | Name length in Bytes (= NL)                                                                                       | Uint16LE |
|           0xA            |     NL      | Name of the file                                                                                                  |  UTF-8   |
|          0xA+NL          |     0x2     | Owner length in Bytes (= OL)                                                                                      | Uint16LE |
|          0xC+NL          |     OL      | Owner of the file                                                                                                 |  UTF-8   |
|        0xC+NL+OL         |     0x2     | Owner group length in Bytes (= GL)                                                                                | Uint16LE |
|        0xE+NL+OL         |     GL      | Owner group of the file                                                                                           |  UTF-8   |
|       0xE+NL+OL+GL       |     0x4     | Web link length in Bytes (= WL)                                                                                   | Uint32LE |
|      0x12+NL+OL+GL       |     WL      | Web link of the file                                                                                              |  UTF-8   |
|     0x12+NL+OL+GL+WL     |     0x6     | File creation time as Unix timestamp (ms)                                                                         | Uint48LE |
|     0x18+NL+OL+GL+WL     |     0x6     | File modification time as Unix timestamp (ms)                                                                     | Uint48LE |
|     0x1E+NL+OL+GL+WL     |     0x6     | File access time as Unix timestamp (ms)                                                                           | Uint48LE |
|     0x24+NL+OL+GL+WL     | 0x1 + 1 bit | File permissions (chmod format)                                                                                   |  binary  |
| 0x25+NL+OL+GL+WL + 1 bit |    7 bit    | File attributes (is folder, is hidden, is system file, enable backup, require backup, is read-only, is main file) |  binary  |

Repeat this for every file.

##### 2.4.3.3. Body

The body consists of multiple files.

A file is structured like this:

| Offset | Length | Description   | Type |
| :----: | :----: | :------------ | :--: |
|  0x0   |   ?    | File contents |      |

## 3. Compression algorithms

|     Name     | IDXD code |
| :----------: | :-------: |
| No algorithm |  `NONE`   |
|     LZMA     |  `LZMA`   |
|   DEFLATE    |  `DFLT`   |

## 4. Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="http://leox.dev"><img src="https://avatars.githubusercontent.com/u/84378319?v=4?s=100" width="100px;" alt="LeoX DEV"/><br /><sub><b>LeoX DEV</b></sub></a><br /><a href="#maintenance-Le0X8" title="Maintenance">🚧</a> <a href="https://github.com/HSSPfile/specification/commits?author=Le0X8" title="Documentation">📖</a></td>
    </tr>
  </tbody>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->
