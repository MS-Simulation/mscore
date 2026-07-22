# mscore — foundation crates for the rustims federation

Pure-Rust, PyO3-free foundation of the [rustims](https://github.com/theGreatHerrLebert/rustims)
ecosystem. Published on crates.io:

| crate | what | crates.io |
|---|---|---|
| [`ms-chem`](./ms-chem) | L0 chemistry leaf: elements, residues, sum formulas, isotopes (CIAAW), UNIMOD, backbone fragments | [ms-chem](https://crates.io/crates/ms-chem) |
| [`mscore`](./mscore) | mass-spec data structures + algorithms; sources all chemistry from `ms-chem` | [mscore](https://crates.io/crates/mscore) |

`mscore` depends on `ms-chem` — one source of truth for chemistry. The equivalence of the two
was proven by a differential-parity suite (see `CHEM_PARITY.md` in rustims) before unification;
that work also surfaced and fixed a selenocysteine-mass bug in the legacy tables.

Extracted from the rustims monorepo (at `4f078a66`). The cross-implementation parity gate against
`timsim-chem` remains in rustims, where all three implementations are available.

Later: `ms-io` (Bruker TDF read/write) joins this foundation repo (R3).
