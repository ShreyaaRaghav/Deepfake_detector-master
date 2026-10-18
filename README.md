# ClearSight — Forensic Media Integrity & Image Restoration

**A full-stack computer vision platform for image and video deepfake analysis, image restoration, and digital media inspection.**

ClearSight brings media authenticity analysis and image enhancement into one web application, with a Chrome extension for convenient access to image-analysis and restoration workflows.

It combines CNN-based image classification, frame-based video inspection, classical image-processing techniques, and an interactive forensic dashboard.

[GitHub Repository](https://github.com/ShreyaaRaghav/Deepfake_detector-master)

---

## Overview

Digital media can be manipulated, compressed, blurred, or degraded during capture and transmission. ClearSight provides a unified interface for exploring two complementary problems: identifying potentially manipulated media and improving the visual quality of degraded images.

The platform includes image deepfake detection, video frame analysis, image restoration controls, experiment tracking, and browser-based access through a Chrome extension.

> **Important:** Deepfake detection provides model-based predictions, not definitive proof of authenticity. Results should be interpreted alongside other forensic evidence.

---

## Features

### 1. Image Deepfake Detection
- CNN-based binary image classification using `deepfake_cnn.keras`.
- Prediction scores for real and potentially manipulated images.
- Structured results displayed through the web interface.
- Designed to provide an accessible workflow for image inspection.

### 2. Video Deepfake Analysis
- Video upload and frame extraction.
- Sampling of up to 20 frames across the video timeline, as configured by the application.
- Frame-level analysis to identify potentially suspicious visual content.
- Temporal inspection interface for reviewing results across sampled frames.

**Technical note:** Frame sampling and aggregation do not, by themselves, establish temporal-model learning. The detection method should be described according to the actual inference implementation.

### 3. Image Restoration & Enhancement
An interactive image-processing pipeline built with OpenCV and Pillow (PIL).

| Technique | Purpose |
|---|---|
| Non-Local Means / Bilateral filtering | Reduce noise while preserving useful image structure |
| CLAHE | Improve local contrast in poorly illuminated or low-contrast regions |
| Unsharp masking | Enhance edges and perceived sharpness |
| Brightness adjustment | Modify image illumination |
| Low-light enhancement | Improve visibility in darker regions |
| Portrait retouching | Apply smoothing and enhancement to portrait images |

The interface offers restoration presets, manual calibration controls, and a before/after comparison view.

The restoration pipeline uses classical computer-vision techniques where implemented; it should not be confused with neural image super-resolution or generative restoration unless those methods are explicitly integrated.

### 4. Interactive Forensic Dashboard
- Experiment-run history and status tracking.
- Synthetic-media flags and result summaries.
- Model and restoration-engine status indicators.
- Degradation categories and experiment metadata.
- Dedicated views for restoration, video comparison, detection, and analysis results.

### 5. Chrome Extension
A Manifest V3 browser extension designed to provide access to ClearSight workflows.

- Popup interface for supported media workflows.
- Right-click image actions for analysis and restoration.
- Communication with the locally running Flask application.

Extension functionality depends on the permissions, supported input types, and endpoints implemented in the current version.

---

## Technology Stack

| Component | Technologies |
|---|---|
| Backend | Python, Flask |
| Image deepfake classification | TensorFlow / Keras, CNN |
| Image processing | OpenCV, Pillow (PIL), NumPy |
| Video processing | OpenCV and frame extraction |
| Frontend | HTML, CSS, JavaScript |
| Browser integration | Chrome Extensions API, Manifest V3 |
| Model artifact | `deepfake_cnn.keras` |

*The stack above reflects the technologies identified for the project. Confirm the exact installed dependencies and inference libraries in `requirements.txt` and the implementation before publishing.*

---

## Application Modules

| Module | Purpose |
|---|---|
| Dashboard | Summarize experiments, model status, and analysis results |
| Restore Image | Enhance degraded images with configurable processing controls |
| Video Comparison | Inspect video frames and review temporal analysis results |
| Detection | Access media authenticity analysis |
| Dataset | Manage or inspect supported datasets and benchmark information |
| Experiments | Review experiment runs and outcomes |
| Degradation | Explore degradation-related configurations |
| Model Weights | Inspect model artifacts and loading status |
| Temporal Analysis | Examine frame-level or sequence-level results |
| Results & ROC | Access evaluation-related views, where supported |

Some dashboard sections may depend on local datasets, configured models, or additional backend functionality.

---

## Supported Media Formats

The application is intended to support the following formats. Actual support depends on the active upload validation and decoding implementation.

**Images:** PNG, JPG, JPEG, WebP, GIF, BMP, TIFF

**Videos:** MP4, WebM, MOV, AVI, MKV

---

## Getting Started

### Prerequisites

- Windows, macOS, or Linux.
- Python installed and available from the terminal.
- Google Chrome, if you want to use the browser extension.
- The model artifact and dependencies required by the application.

Use the Python version compatible with the project's installed dependencies. The existing Windows launcher is configured for Python 3.12.

### 1. Clone the repository

```bash
git clone https://github.com/ShreyaaRaghav/Deepfake_detector-master.git
cd Deepfake_detector-master
```

### 2. Create a virtual environment

**Windows:**

```powershell
py -3.12 -m venv .venv
.venv\Scripts\activate
```

**macOS / Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

If installation fails, check the Python version and the compatibility of the project's TensorFlow, PyTorch, OpenCV, and other dependencies. Do not install both ML frameworks unnecessarily if the active code does not require them.

### 4. Start the application

On Windows, you can use the provided launcher:

```powershell
.\start_server.bat
```

Alternatively, run:

```bash
python app.py
```

If your environment uses the Python launcher:

```powershell
py -3.12 app.py
```

### 5. Open ClearSight

Navigate to:

**http://127.0.0.1:5002**

The server must remain running while you use the web application or its locally hosted API.

---

## Chrome Extension Setup

1. Start the ClearSight backend and confirm that the web application loads.
2. Open `chrome://extensions/` in Google Chrome.
3. Enable **Developer mode**.
4. Click **Load unpacked**.
5. Select the repository's `extension/` folder.
6. Open the extension popup and try a supported workflow.
7. If available, right-click a webpage image and select the corresponding ClearSight action.

### Extension troubleshooting

- Confirm that the backend is running on the port configured in the extension.
- Check the extension's permissions and host permissions in `manifest.json`.
- Inspect the extension service worker and browser console for errors.
- Confirm that the requested image can be accessed and decoded.
- If the extension cannot connect, verify that its API URL matches the backend address.

**Security note:** The local server is intended for development and local use. Do not expose it publicly without appropriate authentication, upload validation, request limits, and other security controls.

---

## API Reference

The following endpoints describe the current interface contract. Confirm the request fields and response structure against `app.py` before relying on them in external clients.

### 1. Media Analysis

`POST /analyze`

Accepts a multipart form containing a media file and a media type.

| Field | Type | Description |
|---|---|---|
| `file` | File | Image or video to analyze |
| `type` | String | `image` or `video` |

Example request:

```bash
curl -X POST http://127.0.0.1:5002/analyze \
  -F "file=@sample.jpg" \
  -F "type=image"
```

The response may contain fields such as:

```json
{
  "status": "success",
  "is_fake": false,
  "confidence": 94.2,
  "fake_probability": 5.8,
  "real_probability": 94.2,
  "message": "Analysis completed"
}
```

**Illustrative response only:** The values above are examples of the response format, not measured model performance. Use the actual response returned by your application.

### 2. Image Restoration

`POST /restore`

Accepts an image and restoration parameters.

| Field | Type | Description |
|---|---|---|
| `file` | File | Input image |
| `mode` | String | Restoration preset |
| `denoise` | Number | Optional denoising strength |
| `sharpen` | Number | Optional sharpening strength |
| `contrast` | Number | Optional contrast adjustment |
| `brightness` | Number | Optional brightness adjustment |

Presets described by the current interface include:

- `auto`
- `denoise`
- `clarity`
- `low_light`
- `portrait`

Example request:

```bash
curl -X POST http://127.0.0.1:5002/restore \
  -F "file=@sample.jpg" \
  -F "mode=auto" \
  -F "denoise=50" \
  -F "sharpen=50" \
  -F "contrast=50" \
  -F "brightness=0"
```

The response can include the restored image and processing metadata, depending on the implementation.

Restoration measurements should be interpreted according to their actual definitions. A displayed sharpness increase or noise-reduction percentage is not necessarily an objective image-quality improvement unless measured against an appropriate reference or validated metric.

---

## Project Structure

```text
Deepfake_detector-master/
├── app.py                  # Flask application and backend routes
├── start_server.bat        # Windows startup script
├── requirements.txt        # Python dependencies
├── deepfake_cnn.keras/     # Image classification model artifact
├── templates/
│   └── index.html          # Web application interface
├── extension/
│   ├── manifest.json       # Chrome extension configuration
│   ├── background.js       # Background and context-menu logic
│   ├── popup.html          # Extension popup
│   ├── popup.js            # Popup interactions
│   ├── analyze.html        # Analysis interface
│   ├── analyze.js          # Analysis interactions
│   └── styles.css          # Extension styling
└── uploads/                # Uploaded or processed media, if configured
```

The structure above is based on the current project layout described in the repository documentation. Optional runtime directories may be created when the application starts.

---

## Limitations & Responsible Use

- **Probabilistic predictions:** A model's confidence score is not a guarantee of authenticity.
- **Generalization:** Detection performance may vary across datasets, manipulation techniques, compression levels, and unseen generators.
- **Video analysis:** Sampling a limited number of frames can miss manipulations between sampled frames.
- **Restoration trade-offs:** Denoising and sharpening can remove details or introduce artifacts. Enhanced images should not be treated as unquestionable forensic evidence.
- **Evaluation:** Reliable performance claims require held-out data, documented datasets, appropriate metrics, and reproducible experiments.
- **Privacy:** Use media you have permission to process. Avoid uploading sensitive or private content to untrusted deployments.
- **Deployment:** The application is designed as a local development project unless additional production security and deployment measures are configured.

ClearSight is an experimental media-analysis and image-processing tool, not a substitute for professional forensic examination.

---

## Future Improvements

Potential directions for extending the project include:

- Benchmarking image detection with precision, recall, F1-score, ROC-AUC, and a confusion matrix.
- Evaluating generalization on unseen manipulation datasets.
- Visualizing frame-level prediction scores and temporal inconsistencies.
- Adding reproducible image-restoration evaluations using PSNR and SSIM where reference images are available.
- Improving experiment reproducibility through saved configurations, dataset metadata, and model-version tracking.
- Adding automated tests for backend routes, file validation, and extension-to-server communication.

These are potential improvements, not claims about existing functionality.

---

## License

Add a `LICENSE` file to the repository before specifying redistribution terms. If the project incorporates pretrained weights, datasets, or third-party code, verify their individual licenses and attribution requirements as well.

---

## Author

**Shreya Raghav**

Computer Science & Engineering (Artificial Intelligence)  
Interested in computer vision, machine learning, and full-stack AI systems.

[GitHub](https://github.com/ShreyaaRaghav)

