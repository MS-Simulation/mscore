# mscore

Core data structures and algorithms for computational mass-spectrometry proteomics, in pure Rust —
no PyO3, no file-format I/O. The middle layer of the
[mscore](https://github.com/MS-Simulation/mscore) foundation: it sources its chemistry tables
from [`ms-chem`](https://crates.io/crates/ms-chem) and is the base that
[`ms-io`](https://crates.io/crates/ms-io) builds on.

## Modules

- **`data`** — spectrum and peptide primitives: `MzSpectrum` and `IndexedMzSpectrum` (filtering,
  resampling to a given resolution, vectorized forms), `MsType`, `PeptideSequence` with
  `[UNIMOD:id]`-annotated sequence parsing, `PeptideIon`, `PeptideProductIon` and
  `PeptideProductIonSeries(Collection)` for a/b/c and x/y/z ion series.

- **`chemistry`** — elements and atomic weights, amino-acid masses and compositions, UNIMOD
  modifications, sum formulas, physical constants, and the m/z, charge and CCS ↔ 1/K0 conversion
  formulas. The underlying tables come from `ms-chem`, so there is a single source of truth.

- **`algorithm`** — peptide mass and composition math (monoisotopic peptide and product-ion masses,
  atomic compositions, protonizable-site counting, charge-state simulation) and the **isotope
  algorithms**: distribution convolution (`convolve`, `convolve_pow`),
  `generate_isotope_distribution` from an atomic composition, averagine pattern and spectrum
  generation, and quadrupole-transmission-dependent fragment isotope distributions.

- **`timstof`** — timsTOF-shaped data structures and instrument models: `RawTimsFrame`,
  `TimsFrame`, `ImsFrame`, `TimsSpectrum`, `TimsSlice` (a range of frames with rayon-parallel
  operations), spectrum processing, collision-energy models (`TimsTofCollisionEnergyDIA`) and
  quadrupole ion transmission (`IonTransmission`, `TimsTransmissionDIA`, `TimsTransmissionDDA`).
  Data structures and models only — reading them off disk is `ms-io`'s job.

- **`simulation`** — annotated counterparts of the above (`MzSpectrumAnnotated`,
  `TimsSpectrumAnnotated`, `TimsFrameAnnotated`) carrying per-peak provenance (`PeakAnnotation`,
  `ContributionSource`), used to build synthetic runs that keep their ground truth.

## License

MIT — see [LICENSE](https://github.com/MS-Simulation/mscore/blob/main/LICENSE).
