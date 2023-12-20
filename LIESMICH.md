<p align="center">
  <img align="center" src="https://img.shields.io/github/stars/HSSPfile?style=for-the-badge&logo=github&color=%23ff0">
  <img align="center" src="https://img.shields.io/github/all-contributors/HSSPfile/specification?label=Contributors&logo=github&style=for-the-badge">

  <h1 align="center">HSSP</h1>
  <h3 align="center">Huge Size Supporting Package</h3>
</p>

---

Willkommen bei der HSSP-Spezifikation!

Falls Sie Funktionen oder Änderungen vorschlagen möchten, können Sie dies gerne tun. Dazu müssen Sie nur einen Issue öffnen.

In anderen Sprachen lesen:

- [🇺🇸 English](README.md)

---

## Inhalt

---

## 0. Über diese Spezifikation

Dies ist die offizielle HSSP-Spezifikation, lizenziert unter der [MIT-Lizenz](LICENSE). Alle Datumsangaben liegen im DIN 5008-Datumsformat an inländische Empfänger (TT.MM.JJJJ) vor. Wenn eine MurmurHash3-Prüfsumme verwendet wird, wird immer die Zahl `822616071` (im Dezimalsystem) als Seed verwendet.

## 1. Einleitung

HSSP wurde entwickelt, um große Dateigrößen zu unterstützen und dabei viel besser zu sein als andere Containerformate wie ZIP, RAR usw.

## 2. Versionen

HSSP gibt es in mehreren Versionen, die jeweils unterschiedliche Funktionen aufweisen.

### 2.1. HSSP v1 (Wrongfolder/WFLD)

| Veröffentlichungsdatum |
| :--------------------: |
|       14.03.2023       |

Wrongfolder ist die erste Version von HSSP. Sie heißt Wrongfolder, weil @Le0X8 einen falschen Ordner der ersten HSSP-Implementierung in JavaScript ([&rarr; offizielle JavaScript-Bibliothek](https://github.com/HSSPfile/js)) veröffentlicht hat.

#### 2.1.1. Funktionen

- Speichern von Dateien
- Verschlüsselung des Inhalts
- Integritätsprüfung
- "Indexdatei" (Sie können eine Hauptdatei des Pakets definieren)

#### 2.1.2. Dateistruktur

Die Dateistruktur von Wrongfolder ist relativ einfach. Sie besteht aus einem 64 Byte großen [Header](#2121-header) und einem [Body](#2122-body).

##### 2.1.2.1. Header

| Offset | Länge | Beschreibung                                                                    |   Typ    |
| :----: | :---: | :------------------------------------------------------------------------------ | :------: |
|  0x0   |  0x4  | Magischer Wert: `SFA\x00`                                                       |  UTF-8   |
|  0x4   |  0x4  | MurmurHash3-Prüfsumme des [body](#2122-body) (Dateien)                          | Uint32LE |
|  0x8   |  0x4  | Anzahl der enthaltenen Dateien                                                  | Uint32LE |
|  0xC   | 0x20  | Passwort-Hash (doppeltes SHA-256) (enthält 0x0, wenn kein Passwort gesetzt ist) |          |
|  0x2C  | 0x10  | Initialisierungsvektor (IV) (enthält 0x0, wenn kein Passwort gesetzt ist)       |          |
|  0x3C  |  0x4  | "Indexdatei" (Hauptdatei des Pakets, z. B. eine LIESMICH-Datei)                 | Uint32LE |

##### 2.1.2.2. Body

... to be continued
