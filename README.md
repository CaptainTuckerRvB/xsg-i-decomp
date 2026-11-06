# Xenosaga Episode I - Orbis PS4 Port

[![Code](https://decomp.dev/OpokXeno/xsg-i-decomp.svg?mode=shield&label=Code)](https://decomp.dev/OpokXeno/xsg-i-decomp)
[![Functions](https://decomp.dev/OpokXeno/xsg-i-decomp.svg?mode=shield&measure=functions&label=Functions)](https://decomp.dev/OpokXeno/xsg-i-decomp)
[![Linked](https://decomp.dev/OpokXeno/xsg-i-decomp.svg?mode=shield&measure=complete_code&label=Linked)](https://decomp.dev/OpokXeno/xsg-i-decomp)

> This project is developed with AI assistance.

## Fork Status and Roadmap

This fork tracks the upstream matching decompilation of **Xenosaga Episode I: Der Wille zur Macht** while evaluating future platform portability and runtime abstraction work.

### Current Repository Status

- 100% published function recovery completed
- Upstream matching decompilation remains the primary source of truth
- No native PS4 port currently exists
- No gameplay, renderer, audio, or platform abstraction work has been merged

### Planned Research Areas

- Native Windows runtime
- Native Linux runtime
- Filesystem abstraction
- Input abstraction
- Audio abstraction
- Rendering abstraction
- OpenOrbis PS4 feasibility research

### Development Workflow

- `main` contains stable repository state
- `develop` receives reviewed integration work
- `feature/*` branches own subsystem changes
- `platform/*` branches own platform-specific code
- `ai/*` branches contain AI-assisted research and prototypes

### Repository Governance

- GitHub Actions validation
- CODEOWNERS review requirements
- Pull request templates
- Architecture guardrails
- AI-assisted development disclosure

Porting goals are exploratory only and should not be interpreted as indicating an existing native port.

---

## Progress

Published source coverage: **7,645 of 7,645** in-scope game functions (**100.000%**).

| Version | Target | Functions | Progress |
| --- | --- | ---: | ---: |
| NTSC-U | `SLUS_204.69` | 3,671 / 3,671 | 100.000% |
| NTSC-U | `OV01.OVL` | 1,081 / 1,081 | 100.000% |
| NTSC-U | `OV02.OVL` | 110 / 110 | 100.000% |
| NTSC-U | `OV10.OVL` | 361 / 361 | 100.000% |
| NTSC-U | `OV11.OVL` | 139 / 139 | 100.000% |
| NTSC-U | `OV12.OVL` | 1,788 / 1,788 | 100.000% |
| NTSC-U | `SSD.IRX` | 442 / 442 | 100.000% |
| NTSC-U | `RSSD.IRX` | 53 / 53 | 100.000% |

[![Progress Map](https://decomp.dev/OpokXeno/xsg-i-decomp.svg?w=950&h=475)](https://decomp.dev/OpokXeno/xsg-i-decomp)

---

## About This Project

A matching decompilation of **Xenosaga Episode I: Der Wille zur Macht** for the PlayStation 2.

The goal is readable C source that reproduces the original machine code. The Sony SDK and public libraries do not need to be recovered.

The project focuses on:

- Accurate source recovery
- Reproducible retail binaries
- Historical compiler preservation
- Documentation of original build processes
- Long-term maintainability

The project does **not** distribute original game code, assets, SDK files, or copyrighted binaries.

---

## Building

The historical compilers are 32-bit Linux executables.

On 64-bit Ubuntu:

```bash
sudo apt install libc6-i386 python3-venv
```

On Windows, use **WSL** and keep the checkout and build on a Linux filesystem.

The historical compilers cannot read some Windows filesystem metadata and may report:

```text
Value too large for defined data type
```

You need three things the repository does not contain:

1. Your own copy of the game
2. The original toolchain
3. A Python environment for the disassembler

---

### 1. The Originals

Extract the following from your legally obtained disc image:

```text
SLUS_204.69
OV01.OVL
OV02.OVL
OV10.OVL
OV11.OVL
OV12.OVL
IOP/
```

Place them in a single directory.

`config/originals.json` contains the expected file sizes and SHA-256 hashes. The build verifies these automatically and rejects incorrect revisions.

This project targets the **NTSC-U** release.

---

### 2. The Toolchain

`config/toolchain-identity.json` documents every compiler, assembler, linker, hash, and provenance record required by the build.

Required tools:

| Tool | Purpose |
| --- | --- |
| `ee-gcc2.96-realconv-lp7` | Game code and runtime compiler |
| `ee-gcc2.9-991111` | SCE SDK scaffold compiler |
| `ee-as-la29-vsqrt` | Primary assembler |
| `ee-as-2.9-plain` | SDK assembler |
| `ps2dev-binutils` | Linker, objcopy, and modern GAS |

Verify installation:

```bash
python3 -B tools/tu/toolchain.py --check
```

The verification process checks:

- Tool locations
- Expected hashes
- Tool provenance
- Required patches

---

### 3. The Disassembler

Create and configure the Python environment:

```bash
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
```

---

### 4. Configure Local Paths

Using environment variables:

```bash
export XENO_GAME_DIR=/path/to/extracted-iso
export XENO_TOOLCHAIN_DIR=/path/to/toolchains
export XENO_SPLAT_PYTHON=$PWD/.venv/bin/python
```

Or create:

`config/toolchain-local.json`

```json
{
  "game_dir": "/path/to/extracted-iso",
  "toolchain_root": "/path/to/toolchains",
  "splat_python": "/path/to/.venv/bin/python"
}
```

Some tools are built locally and may differ byte-for-byte while still producing identical binaries.

Override tool verification when required:

```bash
XENO_TOOLCHAIN_ALLOW=ee-as-la29-vsqrt,ee-as-2.9-plain,ps2dev-binutils
```

---

### 5. Build

Generate build files:

```bash
./configure.py
```

Build:

```bash
ninja
```

Verify matching outputs:

```bash
ninja gate
```

Results are written to:

```text
build/gate.json
```

Success is reported as:

```json
{
  "result": "pass"
}
```

meaning all generated binaries match the originals exactly.

Public builds use:

```text
config/objects/data-carves.json
```

to place constants and jump tables emitted by recovered functions.

---

## Repository Layout

```text
.github/
├── workflows/
├── ISSUE_TEMPLATE/
├── CODEOWNERS
└── pull_request_template.md

docs/
├── architecture/
├── hardware/
└── ps4/

src/
├── core/
├── render/
├── audio/
├── input/
├── filesystem/
└── savedata/

platform/
├── windows/
├── linux/
└── ps4/

tests/

tools/

assets/
```

Subsystem directories support future portability and abstraction efforts while preserving the upstream matching-decomp structure.

---

## Branch Strategy

```text
main
│
├── develop
│
├── feature/*
│   ├── boot
│   ├── renderer
│   ├── audio
│   ├── input
│   ├── filesystem
│   ├── savedata
│   ├── testing
│   └── repository-bootstrap
│
├── platform/*
│   ├── windows
│   ├── linux
│   └── ps4
│
└── ai/*
    ├── research
    ├── reviews
    └── prototypes
```

### Merge Flow

```text
ai/*
    ↓
feature/*
    ↓
develop
    ↓
main
```

---

## Contributing

Pick an object from:

```text
config/objects/<unit>.objects.json
```

that has no recovered source.

Write the C implementation and run:

```bash
ninja
```

A function counts as recovered when:

- The translation unit defines it in C
- The file still matches
- The gate succeeds

Development contributions in this fork should follow:

- Pull requests only
- CODEOWNERS review
- Passing GitHub Actions checks
- Subsystem ownership boundaries
- AI disclosure requirements when applicable

---

## Future Research Areas

The following are exploratory goals and are **not currently implemented**:

- Native Windows runtime support
- Native Linux runtime support
- Asset abstraction
- Filesystem abstraction
- Input abstraction
- Audio abstraction
- Rendering abstraction
- OpenOrbis PS4 research
- Build modernization

These goals must not compromise the matching decompilation baseline.

---

## Acknowledgments

Thanks to the developers and contributors of the projects on which this work relies:

- [splat](https://github.com/ethteck/splat),
  [spimdisasm](https://github.com/Decompollaborate/spimdisasm),
  and [Rabbitizer](https://github.com/Decompollaborate/rabbitizer)
- [m2c](https://github.com/matt-kempster/m2c)
- [decomp-permuter](https://github.com/simonlindholm/decomp-permuter)
- [ps2dev/binutils-gdb](https://github.com/ps2dev/binutils-gdb)
- [SSXModding/ps2-ee-toolchain](https://github.com/SSXModding/ps2-ee-toolchain)
- [decomp.me compiler collection](https://github.com/decompme/compilers)
- [PCSX2](https://github.com/PCSX2/pcsx2)

---

## Disclaimer

A legally obtained copy of the game is required.

This repository does not distribute:

- Original game binaries
- Disc images
- Proprietary SDK files
- Copyrighted game assets

All rights belong to their respective owners.

This project is not affiliated with, sponsored :

- Monolith Soft
- Namco
- Bandai Namco Entertainment
- Sony Interactive Entertainment
- Any associated rights holders
