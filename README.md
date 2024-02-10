# Keyword-Extraction



### Script Overview:
This script is designed to process text data extracted from various document formats (PDF, DOCX) and perform text analysis tasks such as keyword extraction and frequency analysis.

### Prerequisites:
- Python 3.x
- Required Python libraries: `io`, `os`, `pandas`, `numpy`, `PyPDF2`, `rake_nltk`, `docx2txt`, `tkinter`, `re`, `nltk`, `matplotlib`, `PIL`

### Usage:
1. Ensure all required Python libraries are installed.
2. Run the script in a Python environment.
3. Select a document file using the file dialog window.
4. The script will then:
    - Extract text content from the selected document.
    - Remove stopwords and irrelevant words.
    - Perform frequency analysis on the remaining words.
    - Generate CSV files containing word frequency data.
    - Extract keywords using RAKE algorithm.
    - Output keyword lists to separate CSV files.
    
### Script Flow:
1. Import necessary libraries and modules.
2. Prompt user to select a document file using a file dialog window.
3. Extract text content from the selected document and save it to a temporary text file.
4. Remove stopwords and irrelevant words from the text.
5. Perform frequency analysis on the remaining words and generate a CSV file with word frequency data.
6. Extract keywords using RAKE algorithm and save them to a CSV file.
7. Cleanup: Remove temporary text files.
8. The script provides intermediate CSV files for both word frequency and extracted keywords for further analysis.

### Notes:
- Stopwords are removed from the text data to focus on relevant content.
- Word frequency analysis is performed to identify frequently occurring terms.
- Keywords are extracted using the RAKE algorithm, which identifies significant phrases in the text.
- The script outputs CSV files containing word frequency data and extracted keywords, facilitating further analysis or visualization.


