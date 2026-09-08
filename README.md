# Document Scanner OCR

A Python-based document scanner that detects a receipt or document using OpenCV, corrects perspective distortion, and extracts text using Tesseract OCR.

## Features

* Document detection using HSV color space and saturation masking
* Contour detection to find the document boundary
* Four-corner detection using `approxPolyDP`
* Automatic corner ordering
* Perspective transformation to straighten the document
* Grayscale preprocessing for OCR
* Text extraction using Tesseract OCR

## Processing Pipeline

```text
Input Image
    ↓
HSV Conversion
    ↓
Saturation Mask
    ↓
Contour Detection
    ↓
Four Corner Detection
    ↓
Order Corner Points
    ↓
Perspective Transformation
    ↓
Grayscale Conversion
    ↓
Tesseract OCR
    ↓
Extracted Text
```

## Technologies Used

* Python
* OpenCV
* NumPy
* Tesseract OCR
* Pytesseract

## Installation

Install the required Python packages:

```bash
pip install opencv-python numpy pytesseract
```

You also need to install Tesseract OCR on your system.

### Ubuntu / Debian

```bash
sudo apt-get install tesseract-ocr
```

### Google Colab

```bash
!apt-get install -y tesseract-ocr
!pip install pytesseract
```

## Usage

Load an input image and run the document processing pipeline:

```python
document_points = detect_document(image)

if document_points is None:
    raise ValueError("Could not detect the document.")

warped = scan_document(image, document_points)

gray = preprocess_for_ocr(warped)

text = extract_text(gray, psm=6)

print(text)
```

## How It Works

### 1. Document Detection

The image is converted to the HSV color space.

A saturation-based mask is used to separate the document from the colorful background.

### 2. Contour Detection

External contours are detected and sorted by area.

Small contours are ignored, and the algorithm searches for a large contour that can be approximated as a four-sided polygon.

### 3. Corner Ordering

The four detected document corners are ordered as:

```text
Top Left        Top Right

Bottom Left     Bottom Right
```

This ordering is required for perspective transformation.

### 4. Perspective Transformation

The detected document is transformed into a rectangular, front-facing view using:

```text
cv.getPerspectiveTransform()
```

and:

```text
cv.warpPerspective()
```

### 5. OCR

The scanned document is converted to grayscale and passed to Tesseract OCR to extract the text.

The `--psm 6` configuration is used because the document contains multiple lines of text.

## Notes

The preprocessing pipeline was tested with both grayscale and resized images.

For the tested receipt image, grayscale preprocessing produced better OCR results than resizing, so resizing was not included in the final pipeline.

## Future Improvements

* Support for different document backgrounds
* Adaptive thresholding for uneven lighting
* Automatic document detection using deep learning
* Support for multiple languages
* Improved OCR preprocessing
* Automatic saving of scanned documents and extracted text

## Author

Hojjat Kazemi
