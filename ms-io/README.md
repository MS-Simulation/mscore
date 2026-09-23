# ms-io

Bruker **timsTOF** I/O in pure Rust — reading and writing the `.d` / TDF format — plus a 4D
(m/z × ion mobility × retention time × intensity) feature-finding and pseudo-spectrum pipeline on
top of it. Part of the [mscore](https://github.com/MS-Simulation/mscore) foundation; it builds
on [`mscore`](https://crates.io/crates/mscore) for its frame and spectrum data structures.

**Scope:** timsTOF only. No Thermo, SCIEX, mzML or other generic vendor support lives here.

## `data` — TDF read/write

- `TimsDataLoader` reads an `analysis.tdf` / `analysis.tdf_bin` pair either **lazily** (frames
  decompressed on demand) or **fully in memory**, exposing frames and `TimsSlice` ranges.
- Acquisition-aware datasets (`TimsDatasetDIA`, `TimsDatasetDDA`, `AcquisitionMode`) plus the SQL
  metadata readers in `meta`: global metadata, the frame table, DIA window groups and PASEF
  MS/MS info.
- `TdfWriter` goes the other way: it encodes frames (Bruker column packing, LZF/zstd) and emits a
  complete `.d` folder with a DIA or DDA-PASEF schedule.

### Two reader backends

Raw TDF stores TOF indices and scan numbers; turning those into m/z and 1/K0 needs a calibration.
Both routes are available:

1. **Proprietary Bruker SDK** — `BrukerLibTimsDataConverter` loads `libtimsdata.so` / `.dll` at
   runtime via `libloading`. Requires the (non-redistributable) SDK on disk.
2. **SDK-free pure Rust** — `BrukerFormulaConverter`, backed by `MzCalibrator` and
   `MobilityCalibrator`, reimplements both axis calibrations from the coefficients stored in the
   `.tdf` SQLite tables, so no vendor binary is needed. The formulas, their provenance and the
   empirical cross-checks are documented in [`BRUKER_TRANSLATORS.md`](BRUKER_TRANSLATORS.md).

`TimsDataLoader::uses_bruker_sdk()` reports which path a loader took.

## `cluster` — features and pseudo-spectra

An analysis pipeline over the read data: 1D peak picking along the mobility and RT axes
(`ImPeak1D`, `RtPeak1D`), 4D clustering into `ClusterResult1D`, averagine-based isotope-envelope
grouping into charge-assigned `SimpleFeature`s (`AveragineLut`), scoring, and MS1/MS2 pairing into
`PseudoSpectrum`s for downstream search engines. Results serialize through `cluster::io`.

## Build notes

`ms-io` pulls **bundled SQLite** (`rusqlite` with the `bundled` feature), so a working **C
toolchain** is required, and **polars** for dataframe output — expect a non-trivial build.

## License

MIT — see [LICENSE](https://github.com/MS-Simulation/mscore/blob/main/LICENSE).
