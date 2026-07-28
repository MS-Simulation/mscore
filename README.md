# mscore — foundation for the rustims / timsim federation

The low-level foundation of the [rustims](https://github.com/theGreatHerrLebert/rustims) /
[timsim](https://github.com/theGreatHerrLebert/timsim) ecosystem: **PyO3-free Rust crates** (published on
crates.io) **plus a lean Python-bindings wheel** ([`mscorepy`](./mscorepy)) that exposes their MS-general
primitives to Python — the base the [`pepdl`](https://github.com/theGreatHerrLebert/pepdl) deep-learning stack
builds on.

> Note: this repo is no longer pure Rust. The three crates below are PyO3-free; `mscorepy` is a thin pyo3
> wheel *over* them, kept in the same repo so the bindings can't drift from the crates they bind.

## Rust crates (crates.io)

| crate | what | crates.io |
|---|---|---|
| [`ms-chem`](./ms-chem) | L0 chemistry leaf: elements, residues, sum formulas, isotopes (CIAAW), UNIMOD, backbone fragments | [ms-chem](https://crates.io/crates/ms-chem) |
| [`mscore`](./mscore) | mass-spec data structures + algorithms; sources all chemistry from `ms-chem` | [mscore](https://crates.io/crates/mscore) |
| [`ms-io`](./ms-io) | Bruker timsTOF TDF read/write (pure Bruker I/O since 0.2.0) | [ms-io](https://crates.io/crates/ms-io) |

`mscore` depends on `ms-chem` — one source of truth for chemistry (equivalence proven by a differential-parity
suite before unification, which also surfaced and fixed a selenocysteine-mass bug in the legacy tables).
`ms-io` builds on `mscore`.

## Python bindings — [`mscorepy`](./mscorepy)

A **pyo3 wheel over `mscore` + `ms-chem` only** — the MS-general primitives (ProForma tokenizer, peptide +
product-ion series, mass/mz/CCS chemistry, UNIMOD) with **no `ms-io` in its dependency closure**, hence no
TDF/Bruker-file I/O, no bundled SQLite and no polars. (It does bind a few `mscore::timstof` data structures,
e.g. `PyTimsSpectrum` — those are plain in-memory types, not file readers.) It's the deliberately small
shared library that the `pepdl` peptide-DL stack — and its consumers
[`timsim-predict`](https://github.com/theGreatHerrLebert/timsim-predict) and
[`sagepy-rescore`](https://github.com/theGreatHerrLebert/sagepy-rescore) — depend on, instead of the heavy full
connector. Built with maturin; not on PyPI yet (installed from git).

```bash
pip install "mscorepy @ git+https://github.com/theGreatHerrLebert/mscore.git#subdirectory=mscorepy"
```

Extracted from the rustims monorepo; the cross-implementation chemistry parity gate against `timsim-chem`
lives in rustims, where all three implementations are available.
