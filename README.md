# Multi-Core Fiber Splice Alignment

A tool for analyzing multi-core fiber (MCF) splice alignment by detecting marker positions in scan data and calculating the rotational alignment angle between left and right fibers.

## Overview

This project processes scan data from multi-core optical fibers to:
- Detect bright and dark markers within the fiber's inner band
- Calculate the rotational alignment angle between left (L) and right (R) fibers
- Visualize the straightened fiber images with detected markers

## Installation

1. Clone the repository:
```bash
git clone https://github.com/skymanbp/multi-core-fiber-splice-alignment.git
cd multi-core-fiber-splice-alignment
```

2. Install dependencies:
```bash
pip install -r requirements.txt jupyter
```

`requirements.txt` lists the analysis libraries only (numpy, pandas, matplotlib, scipy, scikit-image); the notebook runner is not in it. The notebook also imports `tkinter`, so use a Tk-enabled Python build.

## Usage

1. Place your scan data files in the `test-scan-data/` directory (or modify `DATA_DIR` in the notebook)

2. Open and run the Jupyter notebook:
```bash
jupyter notebook MCF_scan.ipynb
```

3. Execute all cells to perform the analysis. The first cell does `os.chdir(DATA_DIR)` with a relative path, so restart the kernel before running the notebook again — a second run in the same kernel fails at that cell.

## Data Format

The tool expects four text files representing scan data from two viewing directions (X and Y) for both left (L) and right (R) fibers:

- `LX.txt` - Left fiber, X-direction scan
- `LY.txt` - Left fiber, Y-direction scan
- `RX.txt` - Right fiber, X-direction scan
- `RY.txt` - Right fiber, Y-direction scan

Each file should contain:
- **Line 1**: Comma-separated theta (angle) values in degrees
- **Lines 2-3**: Metadata (skipped during processing)
- **Lines 4+**: Comma-separated intensity values forming the scan matrix

## Output

The analysis produces:

1. **Console output**:
   - Inner band edge positions
   - Detected marker positions (bright and dark)
   - Alignment angle calculations
   - Summary table of detection methods

2. **Visualization**:
   - Four-panel plot showing straightened images for each scan file
   - Detected markers overlaid on the images
   - Inner band boundaries

3. **Generated files** (written into `test-scan-data/`, gitignored):
   - `index.csv` - A monotonically incremented run counter
   - `{index}_LX.txt`, `{index}_LY.txt`, etc. - Copies of input data for each run

## Algorithm

The analysis pipeline includes:

1. **Parsing**: line 1 is the theta axis, lines 2-3 are metadata (skipped), lines 4+ form the intensity matrix
2. **Boundary detection**: vertical Sobel gradient with per-column, prominence-gated peak picking, MAD outlier rejection, and smoothing
3. **Image straightening**: per-column sub-pixel vertical shift that centres the boundary midline
4. **Inner band detection**: per-column gradient argmax around the mid-row, with both edges flattened to their column mean
5. **Marker detection**: two-stage — candidate ranking on the mid-band profile under a 180° bright/dark consistency check, then refinement to local extrema of the cumulative-intensity profile
6. **Direction cross-check**: five detectors (Simple / Segmented / Radon / Gabor / Hough) compared against a per-file reference in printed tables, as a data-quality diagnostic
7. **Alignment calculation**: (θ_L + θ_R) mod 360 per scan pairing, 0/360 wrap correction, then ±20°-from-mean outlier screening at whole-direction granularity — the direction (X or Y) owning the worst outlier is dropped if the other direction is then clean, otherwise all available components are retained with a warning — and the median of the retained components

## Legacy Notebook

`legacy/MCF_scan_legacy.ipynb` is a frozen historical copy of an earlier version of the analysis, kept for reference. The pipeline described above runs entirely from `MCF_scan.ipynb`; nothing in the repository imports or executes the legacy notebook.

## License

This project is provided as-is for research and educational purposes.
