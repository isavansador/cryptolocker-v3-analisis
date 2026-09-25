# CryptoLocker v3 — Ransomware Analysis

[🇬🇧 English](#-english) · [🇪🇸 Español](#-español)

---

<a name="-english"></a>
## 🇬🇧 English

Static and dynamic analysis of a CryptoLocker v3 ransomware sample
conducted in an isolated Windows XP SP3 laboratory environment.

> ⚠️ **Educational purpose only.** No malware binaries are hosted in this
> repository. Hashes and indicators are provided for defensive research.

---

### Overview

This project documents the end-to-end analysis of a CryptoLocker v3
sample, from PE inspection and cryptographic routine identification to
behavioral analysis and MITRE ATT&CK mapping. The goal was to answer:

> *How does a cryptographic ransomware work internally, and what is its
> key derivation chain?*

**Author:** Salazar Rivera Jonathan Isaac — Computer Engineering student, UPIICSA (IPN)
**Course:** Ethical Hacking
**Date:** September 2026
**Duration:** 3 days 

---

### Sample

| Field | Value |
|---|---|
| Family | CryptoLocker v3 |
| Format | PE32 executable (GUI), Intel i386 |
| Size | 290,816 bytes |
| Compiler | Visual Studio (2015) |
| Source | darama22/Malware-Research-Hub |
| SHA-256 | `3372c1edab46837f1e973164fa2d726c5c5e17bcb888828ccd7c4dfcc234a370` |
| MD5 | `209a288c68207d57e0ce6e60ebf60729` |

---

### 🧪 Laboratory Environment

- **Host:** Ubuntu Linux
- **Analysis VM:** Windows XP Professional SP3 x86
- **Network:** Fully isolated (no gateway, no NAT, no Internet — verified with `ping`)
- **Snapshot strategy:** `XP-PRE-EJECUCION` taken before execution
- **Evidence transfer:** No executables transferred out of the VM

---

### Tools

**Static:** Ghidra 12.1.3, pefile, file, sha256sum/md5sum
**Dynamic:** Process Monitor 3.20, OllyDbg 1.10, Wireshark 4.6.4, Regedit

---

### 🔐 Key Derivation Chain

```
CryptGenRandom + system info
        ↓
FUN_0041b400 (collect_entropy)
        ↓
FUN_0041ac60 (prng_sha1_mix)
        ↓
FUN_0041af10 (prf_sha1_generate) → 32 bytes
        ↓
FUN_0041cf90 (sha256) → AES-256 key
        ↓
FUN_004212c0 (aes256_key_expansion)
        ↓
FUN_00420d01 + FUN_004204d0 (AES-256-CBC)
        ↓
FUN_00402ac0 (encrypt_file)
```

---

### MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|---|---|---|---|
| Execution | Command and Scripting Interpreter: Windows Command Shell | T1059.003 | `cmd.exe /c del ...` |
| Persistence | Registry Run Keys / Startup Folder | T1547.001 | `HKCU\...\Run\crypto13` |
| Defense Evasion | Obfuscated Files or Information | T1027 | Self-copy with random name |
| Defense Evasion | Indicator Removal on Host | T1070 | Self-deletion of original |
| Defense Evasion | Impair Defenses: Disable or Modify Tools | T1562 | `vssadmin delete shadows` |
| Impact | Data Encrypted for Impact | T1486 | AES-256-CBC encryption |
| Impact | Inhibit System Recovery | T1490 | Shadow copy destruction |

---

###  Repository Structure

- [`informe/`](./informe/) — Full technical report
- [`analisis-estatico/`](./analisis-estatico/) — Static analysis
- [`analisis-dinamico/`](./analisis-dinamico/) — Dynamic analysis
- [`iocs/`](./iocs/) — Indicators of Compromise
- [`mitre-attack/`](./mitre-attack/) — TTP mapping
- [`cadena-criptografica/`](./cadena-criptografica/) — Key derivation
- [`deteccion/`](./deteccion/) — YARA rule
- [`herramientas/`](./herramientas/) — Lab setup guide

---

### Mitigation Recommendations

1. Maintain offline, disconnected backups — the malware destroys `vssadmin` shadow copies.
2. Do not pay the ransom.
3. Isolate infected systems immediately.
4. Deploy EDR with behavioral detection.
5. Block `vssadmin delete shadows` and similar commands.
6. Apply least privilege.
7. Keep systems patched and updated.

---

### Key Learnings

**Technical:** Identifying a full cryptographic chain in a binary;
using Ghidra for static analysis; using Process Monitor and OllyDbg
for dynamic analysis; building a safe isolated lab; documenting IOCs
and TTPs.

**Process:** Continuous documentation; snapshot-before-execution;
verifying safety at every file transfer; patience for complex functions.

**What I'd do differently:** Configure Process Monitor with a backing
file from the start; set a precise breakpoint at `FUN_0041cf90` to dump
`DAT_00445ED0` before it is overwritten; capture more screenshots live.

---

### Disclaimer

This repository is for educational and defensive research purposes only.
No malware binaries are hosted here. The author is not responsible for
misuse of the information provided.

---
---

<a name="-español"></a>
## 🇪🇸 Español

Análisis estático y dinámico de una muestra de ransomware CryptoLocker v3,
realizado en un entorno de laboratorio aislado con Windows XP SP3.

>  **Solo con fines educativos.** En este repositorio no se aloja ningún
> binario de malware. Los hashes e indicadores se proporcionan únicamente
> para investigación defensiva.

---

### Descripción general

Este proyecto documenta el análisis de extremo a extremo de una muestra de
CryptoLocker v3, desde la inspección del PE y la identificación de rutinas
criptográficas hasta el análisis de comportamiento y el mapeo a MITRE
ATT&CK. El objetivo fue responder:

> *¿Cómo funciona internamente un ransomware criptográfico y cuál es su
> cadena de derivación de claves?*

**Autor:** Salazar Rivera Jonathan Isaac — Estudiante de Ingeniería en Informática, UPIICSA (IPN)
**Materia:** Hackeo Ético
**Fecha:** Septiembre 2026
**Duración:** 3 días 

---

###  Muestra

| Campo | Valor |
|---|---|
| Familia | CryptoLocker v3 |
| Formato | PE32 ejecutable (GUI), Intel i386 |
| Tamaño | 290,816 bytes |
| Compilador | Visual Studio (2015) |
| Origen | darama22/Malware-Research-Hub |
| SHA-256 | `3372c1edab46837f1e973164fa2d726c5c5e17bcb888828ccd7c4dfcc234a370` |
| MD5 | `209a288c68207d57e0ce6e60ebf60729` |

---

### Entorno de laboratorio

- **Host:** Ubuntu Linux
- **VM de análisis:** Windows XP Professional SP3 x86
- **Red:** Totalmente aislada (sin gateway, sin NAT, sin Internet — verificado con `ping`)
- **Estrategia de snapshot:** `XP-PRE-EJECUCION` tomado antes de la ejecución
- **Transferencia de evidencia:** Ningún ejecutable salió de la VM

---

### Herramientas

**Estático:** Ghidra 12.1.3, pefile, file, sha256sum/md5sum
**Dinámico:** Process Monitor 3.20, OllyDbg 1.10, Wireshark 4.6.4, Regedit

---

### Cadena de derivación de claves

```
CryptGenRandom + información del sistema
        ↓
FUN_0041b400 (collect_entropy)
        ↓
FUN_0041ac60 (prng_sha1_mix)
        ↓
FUN_0041af10 (prf_sha1_generate) → 32 bytes
        ↓
FUN_0041cf90 (sha256) → clave AES-256
        ↓
FUN_004212c0 (aes256_key_expansion)
        ↓
FUN_00420d01 + FUN_004204d0 (AES-256-CBC)
        ↓
FUN_00402ac0 (encrypt_file)
```

---

### Mapeo MITRE ATT&CK

| Táctica | Técnica | ID | Evidencia |
|---|---|---|---|
| Execution | Command and Scripting Interpreter: Windows Command Shell | T1059.003 | `cmd.exe /c del ...` |
| Persistence | Registry Run Keys / Startup Folder | T1547.001 | `HKCU\...\Run\crypto13` |
| Defense Evasion | Obfuscated Files or Information | T1027 | Auto-copia con nombre aleatorio |
| Defense Evasion | Indicator Removal on Host | T1070 | Auto-eliminación del original |
| Defense Evasion | Impair Defenses: Disable or Modify Tools | T1562 | `vssadmin delete shadows` |
| Impact | Data Encrypted for Impact | T1486 | Cifrado AES-256-CBC |
| Impact | Inhibit System Recovery | T1490 | Destrucción de copias de sombra |

---

### Estructura del repositorio

- [`informe/`](./informe/) — Informe técnico completo
- [`analisis-estatico/`](./analisis-estatico/) — Análisis estático
- [`analisis-dinamico/`](./analisis-dinamico/) — Análisis dinámico
- [`iocs/`](./iocs/) — Indicadores de Compromiso
- [`mitre-attack/`](./mitre-attack/) — Mapeo de TTPs
- [`cadena-criptografica/`](./cadena-criptografica/) — Derivación de claves
- [`deteccion/`](./deteccion/) — Regla YARA
- [`herramientas/`](./herramientas/) — Guía de montaje del laboratorio

---

### Recomendaciones de mitigación

1. Mantener copias de seguridad offline y desconectadas — el malware destruye las copias de sombra con `vssadmin`.
2. No pagar el rescate.
3. Aislar inmediatamente el sistema infectado.
4. Usar soluciones EDR con capacidad de detección de comportamiento.
5. Bloquear la ejecución de `vssadmin delete shadows` y comandos similares.
6. Aplicar el principio de menor privilegio.
7. Mantener los sistemas actualizados y parcheados.

---

### Aprendizajes clave

**Técnicos:** Identificar una cadena criptográfica completa en un binario;
usar Ghidra para análisis estático; usar Process Monitor y OllyDbg para
análisis dinámico; montar un laboratorio aislado seguro; documentar IOCs
y TTPs.

**De proceso:** Importancia de la documentación continua; snapshot antes
de ejecutar; verificar la seguridad en cada transferencia de archivos;
paciencia para analizar funciones complejas.

**Qué haría diferente:** Configurar Process Monitor con backing file desde
el inicio para no perder eventos; poner un breakpoint exacto en
`FUN_0041cf90` para volcar `DAT_00445ED0` antes de que sea sobrescrito;
documentar más capturas de pantalla en el momento.

---

### Aviso legal

Este repositorio tiene fines exclusivamente educativos y de investigación
defensiva. No se aloja ningún binario de malware. El autor no se hace
responsable del uso indebido de la información aquí presentada.

---

### Contacto

- **LinkedIn:** https://www.linkedin.com/in/jonathan-isaac-salazar-rivera
