
 **Persona-Driven Document Intelligence (Round 1B)**, 

---

# Persona-Driven Document Intelligence (Round 1B)

## Overview

The **Persona-Driven Document Intelligence** system processes a collection of PDFs and extracts the most relevant sections based on a provided **persona** and a **job-to-be-done**. It ranks sections and subsections to deliver a structured JSON output tailored to the user’s needs. The tool runs fully offline, on-device, and is optimized for CPU-only environments.  

## Tech Stack

* **Language:** Python 3.9+
* **PDF Parsing:** PyMuPDF (`fitz`)
* **Text Processing:** `re`, `unicodedata`
* **Keyword Extraction & Similarity:** `scikit-learn` (TF-IDF, cosine similarity)
* **Date/Time Handling:** `datetime`
* **Data Format:** JSON

## Dependencies

* Python ≥ 3.9
* PyMuPDF
* scikit-learn
* Standard Library: `os`, `json`, `re`, `time`, `unicodedata`, `datetime`, `collections`

## Why These Libraries

### PyMuPDF (`fitz`)

* Efficient text and layout extraction with font metadata
* Lightweight and Python-native with AMD64 compatibility

### scikit-learn

* Provides optimized TF-IDF vectorization and cosine similarity
* Avoids large embedding models incompatible with offline CPU-only setups

### Python Standard Library

* Handles Unicode normalization, regex-based cleaning, and timestamping without additional dependencies

## Setup & Installation

   **Navigate into the folder**

   ```
   "adobe_1B\app\input"
   ```
   **For inputting PDFs navigate to **
   ```
   "adobe_1B\app\output"
   ```

2. **Build Docker Image**

   ```powershell
   docker build -t adobe_1b .
   ```

3. **Run Extraction**

   ```powershell
   powershell -ExecutionPolicy Bypass -File .\run.ps1
   
   ```

   All `*.pdf` files in `/app/input` will produce `.json` outputs in `/app/output`.

## Usage Example

```powershell
# Place PDFs in ./app/input
"adobe_1B\app\input"

# View output
"adobe_1B\app\output"
```

## How It Works

1. **Block Extraction**
   Uses PyMuPDF to read text spans along with font size, font name, bold flag, and coor

## Usage

```bash
python src/analyze_persona.py \
  --input-dir app/input \
  --output-file challenge1b_output.json \
  --persona "Food Contractor" \
  --job "Prepare a vegetarian buffet-style dinner menu for a corporate gathering, including gluten-free items."
```

* `app/input`: Folder with 3–10 PDF files
* `challenge1b_output.json`: Output JSON file containing metadata, extracted sections, and subsection analysis

## How It Works

1. **Text Cleaning & Normalization**

   * Converts Unicode to ASCII
   * Removes bullets, merges lines, and standardizes dashes

2. **Heading Detection**

   * Detects headings based on title case, all caps, and absence of trailing punctuation
   * Captures multi-line heading continuations

3. **Section Extraction**

   * Groups content under detected headings
   * Ignores sections with fewer than 40 characters

4. **Relevance Scoring**

   * Constructs a TF-IDF corpus of all sections
   * Calculates cosine similarity with persona + job context
   * Applies keyword boosting and penalizes contradictions (e.g., non-vegetarian content)

5. **Diversification**

   * Selects top N sections from distinct documents
   * Fills additional slots based on score

6. **Subsection Summarization**

   * Identifies and scores paragraphs within each top section
   * Extracts the best-matching paragraph and refines it into a standalone summary

7. **JSON Output**

   * Outputs structured JSON with:

     * `metadata`: Files, persona, job, timestamp
     * `extracted_sections`: Ranked section titles with page numbers
     * `subsection_analysis`: Best paragraph per section

## Extensibility

* Replace TF-IDF with BM25 or embedding-based similarity
* Extend persona schema with expertise levels or excluded preferences
* Add multiprocessing support to handle larger document sets efficiently

---
