<p align="center">
  <img align="center" src="https://img.shields.io/github/stars/HSSPfile?style=for-the-badge&logo=github&color=%23ff0&label=Total%20stars">
  <img align="center" src="https://img.shields.io/github/all-contributors/HSSPfile/specification?label=Contributors&logo=github&style=for-the-badge">

  <h1 align="center">HSSP</h1>
  <h3 align="center">Huge Size Supporting Package</h3>
</p>

---

Welcome to the HSSP specification!

If you have any feature requests for future versions, please open an issue.

Read this in other languages:

- [🇩🇪 Deutsch](LIESMICH.md)

---

<p align="center">
  <a href="https://developers.acridotheres.com/formats/hssp">&#x2728; Now also available on Acridotheres for Developers! &#x2728;</a>
</p>

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
  - [2.5. HSSP v5 (Flagged/FLGD)](#25-hssp-v5-flaggedflgd)
    - [2.5.1. New features](#251-new-features)
    - [2.5.2. File structure](#252-file-structure)
      - [2.5.2.1. Header](#2521-header)
      - [2.5.2.2. Flags](#2522-flags)
      - [2.5.2.3. Index](#2523-index)
      - [2.5.2.3. Body](#2524-body)
  - [2.6. HSSP v6 (Separated/SPRD)](#26-hssp-v6-separatedsprd)
    - [2.6.1. New features](#261-new-features)
    - [2.6.2. Removed features](#262-removed-features)
    - [2.6.3. File structure](#263-file-structure)
      - [2.6.3.1. Header](#2631-header)
      - [2.6.3.2. Flags](#2632-flags)
      - [2.6.3.3. Index](#2633-index)
      - [2.6.3.4. Body](#2634-body)
  - [2.7. HSSP v7 (Cryptography Update/CRYP)](#27-hssp-v7-cryptography-updatecryp)
- [3. Compression algorithms](#3-compression-algorithms)
- [4. Encryption algorithms](#4-encryption-algorithms)
- [5. Checksum algorithms](#5-checksum-algorithms)
- [6. Signing algorithms](#6-signing-algorithms)
- [7. Contributors](#7-contributors)

---

## 0. About this specification

This is the official HSSP specification, licensed under the [MIT License](LICENSE). All dates are in ISO 8601 format (YYYY-MM-DD). If a MurmurHash3 checksum is used, it always uses the number `822616071` (decimal) as seed.

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

HSSP Indexed is the first "real update" to HSSP. It now really uses the additional 64 bytes of the header and is more complex than the versions before. Make sure to decrypt the file before decompressing it.

#### 2.4.1. New features

- Indexing
- Compression
- Splitting

#### 2.4.2. Removed features

- "Index file" &rarr; replaced by a "main file" file attribute in the index

#### 2.4.3. File structure

##### 2.4.3.1. Header

| Offset | Length | Description                                                                                                                         |   Type   |
| :----: | :----: | :---------------------------------------------------------------------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                                                                                 |  UTF-8   |
|  0x4   |  0x1   | File standard version (0x4)                                                                                                         |  Uint8   |
|  0x5   |  0x3   | Unused. You can put anything here.                                                                                                  |          |
|  0x8   |  0x4   | Number of contained files                                                                                                           | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256)                                                                                                      |          |
|  0x2C  |  0x10  | Initialization vector (IV)                                                                                                          |          |
|  0x3C  |  0x4   | Used [compression algorithm](#3-compression-algorithms)                                                                             |  UTF-8   |
|  0x40  |  0x4   | Checksum of the [body](#2433-body) (files) and the [index](#2432-index)                                                             | Uint32LE |
|  0x44  |  0x8   | File count of all the files, even those that are not in this package! (may be 0x0 if not splitted)                                  | Uint64LE |
|  0x4C  |  0x8   | Offset of the file that has been splitted. (0x0 if not splitted)                                                                    | Uint64LE |
|  0x54  |  0x4   | Checksum of the previous package (0x0 if not splitted or not existing)                                                              | Uint32LE |
|  0x58  |  0x4   | Checksum of the next package (0x0 if not splitted or not existing)                                                                  | Uint32LE |
|  0x5C  |  0x4   | ID of this package (0x0 if not splitted) (0 is the first part, 1 is the second, ...)                                                | Uint32LE |
|  0x60  |  0x10  | Comment                                                                                                                             |  UTF-8   |
|  0x70  |  0x10  | Generator credits, the official JavaScript library puts `hssp 5.0.0 @ npm` in here. Acridotheres uses something like `core v0.1.0`. |  UTF-8   |

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

### 2.5. HSSP v5 (Flagged/FLGD)

| Release date |
| :----------: |
|  2023-08-24  |

HSSP Flagged is a small, but important update to HSSP Indexed. It adds flags to determine if compression, encryption, splitting, etc. is used, which makes HSSP more robust because "hash equal to zero" bugs cannot happen anymore.

"Hash equal to zero" bugs happend when a parser thought that the file is not encrypted, because the password hash was equal to zero. This is not possible anymore, because the parser can now check securely if the file is encrypted.

#### 2.5.1. New features

- Flags

#### 2.5.2. File structure

##### 2.5.2.1. Header

| Offset | Length | Description                                                                                                                         |   Type   |
| :----: | :----: | :---------------------------------------------------------------------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                                                                                 |  UTF-8   |
|  0x4   |  0x1   | File standard version (0x4)                                                                                                         |  Uint8   |
|  0x5   |  0x3   | [Flags](#2522-flags)                                                                                                                |          |
|  0x8   |  0x4   | Number of contained files                                                                                                           | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256)                                                                                                      |          |
|  0x2C  |  0x10  | Initialization vector (IV)                                                                                                          |          |
|  0x3C  |  0x4   | Used [compression algorithm](#3-compression-algorithms)                                                                             |  UTF-8   |
|  0x40  |  0x4   | Checksum of the [body](#2524-body) (files)                                                                                          | Uint32LE |
|  0x44  |  0x8   | File count of all the files, even those that are not in this package! (may be 0x0 if not splitted)                                  | Uint64LE |
|  0x4C  |  0x8   | Offset of the file that has been splitted. (0x0 if not splitted)                                                                    | Uint64LE |
|  0x54  |  0x4   | Checksum of the previous package (0x0 if not splitted or not existing)                                                              | Uint32LE |
|  0x58  |  0x4   | Checksum of the next package (0x0 if not splitted or not existing)                                                                  | Uint32LE |
|  0x5C  |  0x4   | ID of this package (0x0 if not splitted) (0 is the first part, 1 is the second, ...)                                                | Uint32LE |
|  0x60  |  0x10  | Comment                                                                                                                             |  UTF-8   |
|  0x70  |  0x10  | Generator credits, the official JavaScript library puts `hssp 5.0.0 @ npm` in here. Acridotheres uses something like `core v0.1.0`. |  UTF-8   |

##### 2.5.2.2. Flags

| Bit | Name                             | Description                                                        |
| :-: | :------------------------------- | :----------------------------------------------------------------- |
|  0  | Is encrypted                     | If this bit is set, the file is encrypted.                         |
|  1  | Is compressed                    | If this bit is set, the file is compressed.                        |
|  2  | Is splitted                      | If this bit is set, the file is splitted.                          |
|  3  | Continues previous split package | If this bit is set, the file continues the previous split package. |
|  4  | Continues next split package     | If this bit is set, the file continues the next split package.     |
|  5  | F6                               | Unused.                                                            |
|  6  | F7                               | Unused.                                                            |
|  7  | F8                               | Unused.                                                            |
|  8  | F9                               | Unused.                                                            |
|  9  | F10                              | Unused.                                                            |
| 10  | F11                              | Unused.                                                            |
| 11  | F12                              | Unused.                                                            |
| 12  | F13                              | Unused.                                                            |
| 13  | F14                              | Unused.                                                            |
| 14  | F15                              | Unused.                                                            |
| 15  | F16                              | Unused.                                                            |
| 16  | F17                              | Unused.                                                            |
| 17  | F18                              | Unused.                                                            |
| 18  | F19                              | Unused.                                                            |
| 19  | F20                              | Unused.                                                            |
| 20  | F21                              | Unused.                                                            |
| 21  | F22                              | Unused.                                                            |
| 22  | F23                              | Unused.                                                            |
| 23  | F24                              | Unused.                                                            |

##### 2.5.2.3. Index

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

##### 2.5.2.4. Body

The body consists of multiple files.

A file is structured like this:

| Offset | Length | Description   | Type |
| :----: | :----: | :------------ | :--: |
|  0x0   |   ?    | File contents |      |

### 2.6. HSSP v6 (Separated/SPRD)

| Release date |
| :----------: |
|  2023-08-31  |

This update is another big update to HSSP. It separates the index and the files, which opens possibilities for multithreaded parsing
for a massive performance boost, because files can now be decrypted and decompressed at the same time.

Because of the new chaining feature, the file structure is now much more complex than before, but because this feature, the file generation is now much more flexible.

#### 2.6.1. New features

- Index and files are now encrypted and compressed separately, also every chain now has its own checksum
- Calculation of the index length is now required
- File chaining (files can now be chained for encryption and compression) to bundle multiple files together

#### 2.6.2. Removed features

- Flags F9-F24 because they were unused
- HSSP v4 compression codes because they were longer than they should be

#### 2.6.3. File structure

##### 2.6.3.1. Header

| Offset | Length | Description                                                                                                                         |   Type   |
| :----: | :----: | :---------------------------------------------------------------------------------------------------------------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`                                                                                                                 |  UTF-8   |
|  0x4   |  0x1   | File standard version (0x4)                                                                                                         |  Uint8   |
|  0x5   |  0x1   | [Flags](#2632-flags)                                                                                                                |          |
|  0x6   |  0x2   | Index length in bytes (-IE\*FC) =(IL)                                                                                               | Uint16LE |
|  0x8   |  0x4   | Number of contained files (=FC)                                                                                                     | Uint32LE |
|  0xC   |  0x20  | Password hash (double SHA-256)                                                                                                      |          |
|  0x2C  |  0x10  | Initialization vector (IV)                                                                                                          |          |
|  0x3C  |  0x2   | Used [compression algorithm](#3-compression-algorithms)                                                                             |          |
|  0x3E  |  0x2   | Average index entry length (=IE)                                                                                                    | Uint16LE |
|  0x40  |  0x4   | Checksum of the [index](#2633-index)                                                                                                | Uint32LE |
|  0x44  |  0x8   | File count of all the files, even those that are not in this package! (may be 0x0 if not splitted)                                  | Uint64LE |
|  0x4C  |  0x8   | Offset of the file that has been splitted. (0x0 if not splitted)                                                                    | Uint64LE |
|  0x54  |  0x4   | Checksum of the previous package (0x0 if not splitted or not existing)                                                              | Uint32LE |
|  0x58  |  0x4   | Checksum of the next package (0x0 if not splitted or not existing)                                                                  | Uint32LE |
|  0x5C  |  0x4   | ID of this package (0x0 if not splitted) (0 is the first part, 1 is the second, ...)                                                | Uint32LE |
|  0x60  |  0x10  | Comment                                                                                                                             |  UTF-8   |
|  0x70  |  0x10  | Generator credits, the official JavaScript library puts `hssp 5.0.0 @ npm` in here. Acridotheres uses something like `core v0.1.0`. |  UTF-8   |

##### 2.6.3.2. Flags

| Bit | Name                             | Description                                                                 |
| :-: | :------------------------------- | :-------------------------------------------------------------------------- |
|  0  | Is encrypted                     | If this bit is set, the file is encrypted.                                  |
|  1  | Is compressed                    | If this bit is set, the file is compressed.                                 |
|  2  | Is splitted                      | If this bit is set, the file is splitted.                                   |
|  3  | Continues previous split package | If this bit is set, the file continues the previous split package.          |
|  4  | Continues next split package     | If this bit is set, the file continues the next split package.              |
|  5  | Compression before encryption    | If this bit is set, the file is encrypted before instead after compression. |
|  6  | F7                               | Unused.                                                                     |
|  7  | F8                               | Unused.                                                                     |

##### 2.6.3.3. Index

The index length is calculated like this: `IE \* FC + IL`, this is now required due to index encryption & compression.

The index is structured like this:

|          Offset          |   Length    | Description                                                                                                              |   Type   |
| :----------------------: | :---------: | :----------------------------------------------------------------------------------------------------------------------- | :------: |
|           0x0            |     0x8     | File length in Bytes                                                                                                     | Uint64LE |
|           0x8            |     0x2     | Name length in Bytes (= NL)                                                                                              | Uint16LE |
|           0xA            |     NL      | Name of the file                                                                                                         |  UTF-8   |
|          0xA+NL          |     0x2     | Owner length in Bytes (= OL)                                                                                             | Uint16LE |
|          0xC+NL          |     OL      | Owner of the file                                                                                                        |  UTF-8   |
|        0xC+NL+OL         |     0x2     | Owner group length in Bytes (= GL)                                                                                       | Uint16LE |
|        0xE+NL+OL         |     GL      | Owner group of the file                                                                                                  |  UTF-8   |
|       0xE+NL+OL+GL       |     0x4     | Web link length in Bytes (= WL)                                                                                          | Uint32LE |
|      0x12+NL+OL+GL       |     WL      | Web link of the file                                                                                                     |  UTF-8   |
|     0x12+NL+OL+GL+WL     |     0x6     | File creation time as Unix timestamp (ms)                                                                                | Uint48LE |
|     0x18+NL+OL+GL+WL     |     0x6     | File modification time as Unix timestamp (ms)                                                                            | Uint48LE |
|     0x1E+NL+OL+GL+WL     |     0x6     | File access time as Unix timestamp (ms)                                                                                  | Uint48LE |
|     0x24+NL+OL+GL+WL     | 0x1 + 1 bit | File permissions (chmod format)                                                                                          |  binary  |
| 0x25+NL+OL+GL+WL + 1 bit |    7 bit    | File attributes (is folder, is hidden, is system file, enable backup, require backup, is read-only, is main file)        |  binary  |
|     0x26+NL+OL+GL+WL     |     0x1     | File attributes 2 (is encrypted, is compressed, connected for encryption, connected for compression, 4 unallocated bits) |  binary  |
|     0x27+NL+OL+GL+WL     |     0x4     | Chain MurmurHash3 checksum                                                                                               |  binary  |
|     0x2B+NL+OL+GL+WL     |     0x8     | File length after encryption & compression                                                                               | Uint64LE |

Repeat this for every file.

##### 2.6.3.4. Body

The body consists of multiple files.

A file is structured like this:

| Offset | Length | Description   | Type |
| :----: | :----: | :------------ | :--: |
|  0x0   |   ?    | File contents |      |

### 2.7. HSSP v7 (Cryptography Update/CRYP)

| Release date |
| :----------: |
|    soon!     |

This large update adds the option to use a different encryption algorithm than AES-256-CBC, signing, asymmetric encryption support and native support for [Acridotheres WebAuth](https://acridotheres.com/plus/webauth) as well as better header organization & even more checksums to verify integrity even better.

> [!WARNING]
> HSSP7 has a very different header structure compared to the previous versions.

### 2.7.1. New features

- Added support for different encryption algorithms
- Added support for signing
- Added WebAuth support
- Added support for multiple compression & encryption algorithms in one file
- Optimized header structure
- Comments are optional and can be up to 65536 bytes long
- Generator credits are now replaced by a generator ID (e. g. `acridotheres.com` &rarr; `0x4163`) and can be optional (by setting the ID to `0x0`).

### 2.7.2. Start Header

The archive starts with a 9 byte header.

| Offset | Length | Description                 |   Type   |
| :----: | :----: | :-------------------------- | :------: |
|  0x0   |  0x4   | Magic value: `HSSP`         |  UTF-8   |
|  0x4   |  0x1   | File standard version (0x7) |  Uint8   |
|  0x5   |  0x4   | Main header checksum        | Uint32LE |

### 2.7.3. Main Header

Most of the header fields from the previous versions are now in the main header.

| Offset | Length  | Description                                                                               |   Type   |
| :----: | :-----: | :---------------------------------------------------------------------------------------- | :------: |
|  0x0   |   0x1   | Flags                                                                                     |          |
|  0x1   | 0x0/0x2 | Index length in bytes (-IE\*FC) =(IL); only set if the archive is encrypted or compressed | Uint16LE |
|        |   0x4   | Number of contained files                                                                 | Uint32LE |
|        | 0x0/0x2 | Average index entry length (=IE); only set if the archive is encrypted or compressed      | Uint16LE |
|        |   0x4   | Checksum of the index                                                                     | Uint32LE |
|        |   0x4   | Chain count                                                                               | Uint32LE |
|        |   0x4   | Checksum of the chain list                                                                | Uint32LE |
|        | 0x0/0x4 | Splitting header checksum; only set if the archive is splitted                            | Uint32LE |
|        | 0x0/0x4 | Number of encryption headers; only set if the archive is encrypted                        | Uint32LE |
|        | 0x0/0x4 | Checksum of the first encryption header; only set if the archive is encrypted             | Uint32LE |
|        | 0x0/0x4 | Number of compression headers; only set if the archive is compressed                      | Uint32LE |
|        | 0x0/0x4 | Checksum of the first compression header; only set if the archive is compressed           | Uint32LE |
|        | 0x0/0x4 | Number of signing headers; only set if the archive is signed                              | Uint32LE |
|        | 0x0/0x4 | Checksum of the first signing header; only set if the archive is signed                   | Uint32LE |

### 2.7.4. Flags

When bits 2 and 3 are set, the file is not splitted.

| Bit | Name                             | Description                                                           |
| :-: | :------------------------------- | :-------------------------------------------------------------------- |
|  0  | Is encrypted                     | If this bit is set, the file is encrypted.                            |
|  1  | Is compressed                    | If this bit is set, the file is compressed.                           |
|  2  | Is first                         | If this bit is set, the file is the first part of a splitted archive. |
|  3  | Is last                          | If this bit is set, the file is the last part of a splitted archive.  |
|  4  | Continues previous split package | If this bit is set, the file continues the previous split package.    |
|  5  | Continued in next split package  | If this bit is set, the file continues the next split package.        |
|  6  | Is signed                        | If this bit is set, the file is signed.                               |
|  7  | Uses WebAuth                     | If this bit is set, the file uses WebAuth.                            |

### 2.7.5. Splitting Header

The splitting header is only present if the archive is splitted.

| Offset | Length  | Description                                                                     |   Type   |
| :----: | :-----: | :------------------------------------------------------------------------------ | :------: |
|  0x0   | 0x0/0x4 | Checksum of the previous archive; only set if the archive is not the first part | Uint32LE |
|        | 0x0/0x4 | Checksum of the next archive; only set if the archive is not the last part      | Uint32LE |
|        | 0x0/0x8 | Continued file length; only set if the archive is not the last part             | Uint64LE |
|        | 0x0/0x8 | Continuing file length; only set if the archive is not the first part           | Uint64LE |

### 2.7.6. Encryption Headers

The encryption headers are only present if the archive is encrypted.

| Offset | Length  | Description                                                                                                                  |   Type   |
| :----: | :-----: | :--------------------------------------------------------------------------------------------------------------------------- | :------: |
|  0x0   |   0x4   | Encryption method ID (to identify encrption config later in the file)                                                        | Uint32LE |
|  0x4   |   0x2   | Encryption algorithm (see [here](#4-encryption-algorithms))                                                                  | Uint16LE |
|  0x6   |   0x2   | Key checksum algorithm (see [here](#5-checksum-algorithms))                                                                  | Uint16LE |
|  0x8   |    ?    | Key checksum, length depends on the algorithm                                                                                |          |
|        |    ?    | IV, length depends on the algorithm                                                                                          |          |
|        | 0x0/0x4 | WebAuth URL length; only set if the archive uses WebAuth (has to be set to 0x0 if WebAuth is not used for this method) (=UL) | Uint32LE |
|        | 0x0/UL  | WebAuth URL                                                                                                                  |  UTF-8   |
|        | 0x0/0x4 | Next encryption header checksum; only set if there is another encryption header                                              | Uint32LE |

### 2.7.7. Compression Headers

The compression headers are only present if the archive is compressed.

| Offset | Length | Description                                                                       |   Type   |
| :----: | :----: | :-------------------------------------------------------------------------------- | :------: |
|  0x0   |  0x2   | Compression method ID (to identify compression config later in the file)          | Uint32LE |
|  0x2   |  0x2   | Compression algorithm (see [here](#3-compression-algorithms))                     | Uint16LE |
|  0x4   |  0x4   | Next compression header checksum; only set if there is another compression header | Uint32LE |

### 2.7.8. Signing Headers

The signing headers are only present if the archive is signed.

| Offset | Length  | Description                                                               |   Type   |
| :----: | :-----: | :------------------------------------------------------------------------ | :------: |
|  0x0   |   0x4   | Signing method ID (to identify signing config later in the file)          | Uint32LE |
|  0x4   |   0x2   | Signing algorithm (see [here](#6-signing-algorithms))                     | Uint16LE |
|  0x6   |   0x4   | Public key length (=KL)                                                   | Uint32LE |
|  0xA   |   KL    | Public key                                                                |          |
| 0xA+KL | 0x0/0x4 | Next signing header checksum; only set if there is another signing header | Uint32LE |

### 2.7.9. Chain list

The chain list is a list of all chains in the archive.

| Offset | Length | Description          |   Type   |
| :----: | :----: | :------------------- | :------: |
|  0x0   |  0x4   | Chain ID             | Uint32LE |
|  0x4   |  0x4   | Chain checksum       | Uint32LE |
|  0x8   |  0x4   | Chain encryption ID  | Uint32LE |
|  0xC   |  0x2   | Chain compression ID | Uint32LE |
|  0xE   |  0x4   | Chain signing ID     | Uint32LE |
|  0x12  |  0x4   | Chain key checksum   | Uint32LE |

### 2.7.10. Index

The index starts like this:

| Offset | Length | Description    |   Type   |
| :----: | :----: | :------------- | :------: |
|  0x0   |  0x4   | Encryption ID  | Uint32LE |
|  0x4   |  0x4   | Compression ID | Uint32LE |
|  0x8   |  0x4   | Signing ID     | Uint32LE |

A file in the index is structured like this:

|          Offset          |   Length    | Description                                                                                                              |   Type   |
| :----------------------: | :---------: | :----------------------------------------------------------------------------------------------------------------------- | :------: |
|           0x0            |     0x8     | File length in Bytes                                                                                                     | Uint64LE |
|           0x8            |     0x2     | Name length in Bytes (= NL)                                                                                              | Uint16LE |
|           0xA            |     NL      | Name of the file                                                                                                         |  UTF-8   |
|          0xA+NL          |     0x2     | Owner length in Bytes (= OL)                                                                                             | Uint16LE |
|          0xC+NL          |     OL      | Owner of the file                                                                                                        |  UTF-8   |
|        0xC+NL+OL         |     0x2     | Owner group length in Bytes (= GL)                                                                                       | Uint16LE |
|        0xE+NL+OL         |     GL      | Owner group of the file                                                                                                  |  UTF-8   |
|       0xE+NL+OL+GL       |     0x4     | Web link length in Bytes (= WL)                                                                                          | Uint32LE |
|      0x12+NL+OL+GL       |     WL      | Web link of the file                                                                                                     |  UTF-8   |
|     0x12+NL+OL+GL+WL     |     0x6     | File creation time as Unix timestamp (ms)                                                                                | Uint48LE |
|     0x18+NL+OL+GL+WL     |     0x6     | File modification time as Unix timestamp (ms)                                                                            | Uint48LE |
|     0x1E+NL+OL+GL+WL     |     0x6     | File access time as Unix timestamp (ms)                                                                                  | Uint48LE |
|     0x24+NL+OL+GL+WL     | 0x1 + 1 bit | File permissions (chmod format)                                                                                          |  binary  |
| 0x25+NL+OL+GL+WL + 1 bit |    7 bit    | File attributes (is folder, is hidden, is system file, enable backup, require backup, is read-only, is main file)        |  binary  |
|     0x26+NL+OL+GL+WL     |     0x1     | File attributes 2 (is encrypted, is compressed, connected for encryption, connected for compression, 4 unallocated bits) |  binary  |
|     0x27+NL+OL+GL+WL     |     0x4     | Chain ID                                                                                                                 |  binary  |
|     0x2B+NL+OL+GL+WL     |     0x8     | File length after encryption & compression                                                                               | Uint64LE |
|     0x33+NL+OL+GL+WL     |     0x4     | File checksum                                                                                                            | Uint32LE |

### 2.7.11. Body

The body consists of multiple files.

A file is structured like this:

| Offset | Length | Description   | Type |
| :----: | :----: | :------------ | :--: |
|  0x0   |   ?    | File contents |      |

## 3. Compression algorithms

|     Name     | IDXD/FLGD code |   SPRD/CRYP code   |
| :----------: | :------------: | :----------------: |
| No algorithm |     `NONE`     | _not used anymore_ |
|     LZMA     |     `LZMA`     |      `0x4950`      |
|   DEFLATE    |     `DFLT`     |      `0x4446`      |

## 4. Encryption algorithms

|    Name     | CRYP code | Symmetric | IV length |
| :---------: | :-------: | :-------: | :-------: |
| AES-256-CBC | `0x4136`  |    Yes    |   0x10    |

## 5. Checksum algorithms

|  Name   | CRYP code | Length |
| :-----: | :-------: | :----: |
| SHA-256 | `0x5336`  |  0x20  |
| SHA-512 | `0x5332`  |  0x40  |

## 6. Signing algorithms

|     Name      | CRYP code |
| :-----------: | :-------: |
| RSA + SHA-512 | `0x5236`  |

## 7. Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="http://leox.dev"><img src="https://avatars.githubusercontent.com/u/84378319?v=4?s=100" width="100px;" alt="Leonard Lesinski"/><br /><sub><b>Leonard Lesinski</b></sub></a><br /><a href="#maintenance-Le0X8" title="Maintenance">🚧</a> <a href="https://github.com/HSSPfile/specification/commits?author=Le0X8" title="Documentation">📖</a></td>
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
