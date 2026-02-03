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
git clone https://github.com/yourusername/multi-core-fiber-splice-alignment.git
cd multi-core-fiber-splice-alignment
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

## Usage

1. Place your scan data files in the `test-scan-data/` directory (or modify `DATA_DIR` in the notebook)

2. Open and run the Jupyter notebook:
```bash
jupyter notebook MCF_scan.ipynb
```

3. Execute all cells to perform the analysis

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

3. **Generated files**:
   - `index.csv` - Tracks analysis run count
   - `{index}_LX.txt`, `{index}_LY.txt`, etc. - Copies of input data for each run

## Algorithm

The analysis pipeline includes:

1. **Image preprocessing**: Gaussian smoothing and sharpening
2. **Edge detection**: Sobel gradient-based fiber boundary detection
3. **Image straightening**: Aligning the fiber center line
4. **Inner band detection**: Locating the core region
5. **Marker detection**: Finding bright/dark markers using amplitude analysis
6. **Alignment calculation**: Computing rotational offset from marker positions

## License

This project is provided as-is for research and educational purposes.
