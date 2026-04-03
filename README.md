# PDF Splitter

A simple web application to split PDF files into individual pages.

## Features

- 📤 **Drag & Drop Upload** - Simply drag and drop your PDF file
- ⚡ **Fast Processing** - Split PDFs in seconds
- 📦 **Batch Download** - Download all pages as a ZIP file
- 📄 **Individual Downloads** - Download specific pages separately
- 🔒 **Secure** - Files are processed locally on your machine
- 📱 **Responsive Design** - Works on desktop and mobile

## Installation

1. Navigate to the app directory:
```bash
cd playground/pdf-splitter
```

2. Install the required dependencies:
```bash
pip install -r requirements.txt
```

## Usage

1. Start the Flask server:
```bash
python app.py
```

2. Open your browser and navigate to:
```
http://localhost:5000
```

3. Upload your PDF file by:
   - Dragging and dropping into the upload area
   - Or clicking to browse and select a file

4. Click "Split PDF" to process the file

5. Download options:
   - **Download All Pages (ZIP)** - Get all pages in a single ZIP file
   - **Individual Pages** - Click "Download" on any specific page

## Requirements

- Python 3.7+
- Flask
- pypdf

## File Structure

```
pdf-splitter/
├── app.py              # Flask application
├── requirements.txt    # Python dependencies
├── README.md          # This file
├── templates/
│   └── index.html     # Web interface
├── uploads/           # Temporary upload directory
└── output/            # Split PDF output directory
```

## Notes

- Maximum file size: 50MB
- Only PDF files are accepted
- Files are stored temporarily and may be cleaned up periodically
