# FruitScope — Fruit Segmentation for Automated Fruit Counting

**A complete, local VS Code project** for fruit-region segmentation, estimated object counting, dataset batch processing, visual analysis, and exportable reports.

## What is included
- Creative Streamlit dashboard with a polished forest-green/lime visual theme.
- Image upload and dataset-folder browsing.
- Five classical segmentation modes: Auto, GrabCut, color/contrast, HSV, and LAB.
- Morphological cleanup, minimum-region filtering, and optional watershed separation.
- Object labels, contour overlays, foreground mask, pixel-area measurements, and coverage estimate.
- Batch processing of a folder of images with a CSV report.
- Export bundle with mask, overlay, boundaries, measurements, and a text summary.
- Detailed academic project report guide and troubleshooting notes.
- No API key, LLM, internet service, or model training is needed for the built-in baseline.

## Folder structure
```text
FruitScope_Fruit_Segmentation/
├── app.py                   # Streamlit user interface
├── requirements.txt         # Python dependencies
├── README.md                # Setup and user guide
├── PROJECT_REPORT.md        # Detailed academic documentation
├── launch.bat               # Windows convenience launcher
├── .gitignore
├── src/
│   ├── __init__.py
│   └── segmenter.py          # Image processing and counting pipeline
├── dataset/                  # Add your own images here
├── outputs/                  # Latest generated image outputs
└── tests/
    └── test_segmenter.py     # Basic smoke tests
```

## Requirements
- Windows 10/11
- Python 3.10–3.12 recommended
- VS Code with the Python extension

## Run in VS Code (Windows)
1. Download and extract the project ZIP.
2. In VS Code, select **File → Open Folder** and choose the extracted `FruitScope_Fruit_Segmentation` folder. The folder you open must directly contain `app.py` and `requirements.txt`.
3. Open **Terminal → New Terminal**.
4. Create a virtual environment:
   ```powershell
   py -3.12 -m venv .venv
   ```
   If that command is not available, try `py -m venv .venv`.
5. Activate it:
   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```
   If PowerShell blocks scripts, run `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` in that terminal, then activate again.
6. Install packages:
   ```powershell
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   ```
7. Add dataset images (see next section).
8. Launch:
   ```powershell
   python -m streamlit run app.py
   ```
   Open the local URL printed in the terminal, commonly `http://localhost:8501`.

### One-click Windows option
Double-click `launch.bat`. It creates a virtual environment if needed, installs requirements, and launches the app. The first install requires internet access to download Python packages.

## Add your dataset later
Place images under `dataset/`. Both flat and nested layouts work:
```text
dataset/
├── fruit_photo_01.jpg
├── fruit_photo_02.png
├── apples/
│   └── apples_001.jpg
└── mixed_fruit/
    └── basket_001.jpg
```
Supported formats: `.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`. Labels and annotations are not required for the built-in classical image-processing methods. To use images stored elsewhere, use **Upload image** in the app.

## Use the app
### Single image analysis
1. Choose **Single image analysis** in the sidebar.
2. Upload a photo or choose an image from `dataset/`.
3. Set a segmentation method and sensitivity.
4. Set the minimum connected-region area in pixels.
5. Enable or disable **Separate touching fruits**.
6. Click **Segment & count fruits**.
7. Review overview, mask, processing stages, and object measurements.
8. Download the complete result bundle or CSV.

### Dataset batch analysis
1. Place several images under `dataset/`.
2. Select **Dataset batch analysis**.
3. Set parameters in the sidebar.
4. Click **Run batch analysis**.
5. Download the CSV with per-image count estimates and foreground coverage.

## Which segmentation mode should I try?
- **Auto (recommended):** tries foreground extraction and combines it with color/contrast evidence; it falls back when the combination is too restrictive.
- **GrabCut foreground:** useful when the subject is a foreground object and the background is reasonably distinct.
- **Color / contrast mask:** a flexible baseline when fruit pixels differ in color or local contrast from the background.
- **HSV threshold:** emphasizes saturation and local contrast; may miss low-saturation fruit.
- **LAB color distance:** estimates local color differences in LAB space; results depend on background and lighting.

There is no universal setting for all fruit datasets. Inspect the mask and tune the method, sensitivity, and minimum area on representative images.

## Important scientific limitation
This project is a **classical computer-vision baseline**, not a trained neural-network detector. It is deliberately independent of external model downloads and API keys so it is straightforward to run in VS Code. Its estimates can be inaccurate on cluttered scenes, fruit with colors similar to the background, strong shadows, severe overlap, fruit cut off at the edge, or unusual lighting. Watershed may under-split or over-split touching fruit. Always validate against manually checked counts before reporting accuracy.

For a robust academic evaluation, manually record ground-truth counts for a representative sample and compare:
- Absolute count error: `abs(predicted_count - ground_truth_count)`
- Mean absolute error (MAE): average absolute count error
- Mask IoU: `intersection(mask_pred, mask_gt) / union(mask_pred, mask_gt)`
- Dice coefficient: `2 * intersection / (predicted_pixels + ground_truth_pixels)`

## Troubleshooting
- **`streamlit` is not recognized:** activate `.venv` and run `python -m streamlit run app.py`.
- **`No module named cv2`:** run `pip install -r requirements.txt` in the activated environment.
- **The dataset picker is empty:** confirm images are inside the project's `dataset/` directory; nested folders are supported.
- **The mask includes background:** try GrabCut or LAB, lower sensitivity, and increase minimum area.
- **Fruit pixels are missing:** try Color / contrast or HSV, raise sensitivity, or reduce minimum area.
- **Touching fruit is counted as one:** enable watershed; a visible boundary or shape separation is needed for reliable splitting.
- **The app opens the wrong folder:** reopen the directory that directly contains `app.py`.

## Run the smoke test
After dependencies are installed:
```powershell
python -m unittest discover -s tests -v
```
