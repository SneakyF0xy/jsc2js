# jsc2js

## Overview

`jsc2js` reverses **V8‑generated JSC bytecode** into readable (approximate) JavaScript.

The repository contains two major parts:

1. **Patched d8**: Multiple V8 versions (see Releases) with an added builtin helper `loadjsc()` that loads and prints `.jsc` bytecode.
2. **Integrated View8 tool (based on a customized fork of [suleram/View8](https://github.com/suleram/View8))**: Converts the textual bytecode dump emitted by d8 into an approximate JavaScript reconstruction.

---

## Quick Start

### 1. Get the matching d8

Go to the Releases page and choose the V8 version that is **identical to** the one that produced your target `.jsc`. (Open an Issue if the version you need is missing.)

Each release provides:
- `d8-linux`: Linux 64‑bit executable
- `d8-windows.exe`: Windows 64‑bit executable

### 2. Convert `.jsc` into a bytecode text listing

```bash
./d8 -e "loadjsc('path/to/xxx.jsc')" > xxx.txt
```

Notes:
- `loadjsc()` is the injected helper in the patched d8.
- `xxx.txt` is a human‑readable (though still low‑level) V8 Ignition bytecode disassembly.

### 3. Use the modified View8 to reconstruct JavaScript

A customized `View8/` directory is included:

```bash
cd View8
python view8.py --disassembled ../xxx.txt ../xxx.js
```

Result:
- `xxx.js` contains an approximate JavaScript reconstruction based on the bytecode analysis.  
  (Identifiers / control flow may differ from the original source; this is a semantic approximation.)

### 4. Requirements

View8 requires:
- Python 3.9+ (recommended)
- Dependencies:
  ```bash
  pip install -r requirements.txt
  ```

---

## Notes

- V8 bytecode opcodes, register/slot layouts, and handler table structures vary across versions. Always use a **matching** d8 build.
- Because there is no Node.js environment, the JSC compiled by Node.js may not be decompiled normally, while Electron works fine.
- If the output looks wrong:
  1. Re‑check the version alignment.
  2. If still incorrect, open an Issue.
  3. Or adjust the opcode mapping tables inside the modified View8.
- JavaScript reconstruction cannot be 100% identical to the original:
  - Variable / function names may be missing or replaced.
  - Control flow may be structurally reorganized.
  - Constant folding / runtime optimizations are not perfectly reversible.

---

## References & Acknowledgments

- View8:
  - [suleram/View8](https://github.com/suleram/View8) (integrated and modified here)
- Blog posts / materials:
  - https://guage.cool/wiz-license.html
  - https://rce.moe/2025/01/07/v8-bytecode-decompiler/
- Official V8 project & documentation (Chromium / v8.dev)
