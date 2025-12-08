# MCP PDF Flow (Simple PDF Extractor)

[中文](README.md) | **English**

A powerful MCP (Model Context Protocol) document processing server that bridges LLMs with local documents.

It not only **precisely extracts** text and images from PDFs into **structured Markdown**, but also supports **seamless format conversion** between Markdown, Word (.docx), and PDF.

## ✨ Features

*   **📝 Intelligent Text Extraction**:
    *   Precisely extracts text from PDF pages.
    *   **Smart Markdown Recognition**: Automatically identifies headers, lists, and paragraph structures, merges cross-line text, and outputs clean Markdown.
*   **🖼️ Image Extraction**:
    *   Extracts all images within pages.
    *   **Auto-save**: Extracted images are automatically saved to `extracted_images/<filename>/` in the running directory.
    *   **Preview Support**: Returns Base64 encoding, supporting direct preview in MCP clients like Claude.
*   **🔄 Format Conversion**:
    *   **Markdown to Word**: Converts generated Markdown reports into perfectly formatted Word (.docx) documents with one click.
    *   **Word to PDF**: Supports converting Word documents to PDF files.
        *   *Auto-adapt*: Prioritizes Microsoft Word; falls back to WPS Office if not installed.
*   **⚙️ Flexible Extraction**: Supports extracting all pages, specific page ranges (e.g., `1`, `1-5`), or smart search by keywords.
*   **ℹ️ Metadata Retrieval**: Supports retrieving PDF title, author, page count, and **Table of Contents (TOC)**.

## 🛠️ Requirements

*   **OS**: Windows (Recommended for Word/WPS conversion support) / macOS / Linux
*   **Python**: >= 3.10
*   **Office Software** (Required only for Word to PDF conversion):
    *   Microsoft Word (Best compatibility)
    *   Or WPS Office (Windows supported)

## 📦 Installation & Usage

This project uses `uv` for package management.

### 1. Clone the Project
```bash
git clone https://github.com/Dublin1231/mcp-pdf-flow.git
cd mcp-pdf-flow
```

### 2. Install Dependencies
```bash
uv sync
```

## 🔌 Claude Desktop Configuration

To add this tool to Claude Desktop, edit the configuration file:

*   **Windows**: `%AppData%\Claude\claude_desktop_config.json`
*   **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

Add the following content (Please **ensure** to change the path to your actual local path):

```json
{
  "mcpServers": {
    "simple-pdf": {
      "command": "uv",
      "args": [
        "--directory",
        "D:/path/to/your/mcp-pdf-flow", 
        "run",
        "simple-pdf"
      ],
      "env": {
        "PYTHONIOENCODING": "utf-8"
      }
    }
  }
}
```
*Note: Windows users please use `/` or `\\` as path separators.*

## 💬 How to Use in Chat

**You don't need to manually fill in JSON arguments!** Just tell Claude what you want to do in the chat, and it will automatically call the tools.

### Scenario Examples

#### 1. Read PDF
*   **Specify Absolute Path**:
    > **User**: "Read pages 1 to 5 of `D:\Documents\report.pdf` for me."
*   **Read file in project directory**:
    > **User**: "Read `test.pdf` in the current directory."
    > *(Note: Claude will automatically look for files in the running directory)*

#### 2. Convert Markdown to Word
> **User**: "Save the extracted content as a Word document at `D:\Documents\output.docx`."
>
> **Claude**: (Automatically calls `convert_markdown_to_docx` tool)

#### 3. Word to PDF
> **User**: "Convert `D:\Documents\draft.docx` to PDF."
>
> **Claude**: (Automatically calls `convert_docx_to_pdf` tool)

---

## 📖 Tool List

> **💡 Tip: How to get "Absolute Path"?**
> *   **Windows**: Hold `Shift` and right-click the file, select "Copy as path". Remove the surrounding quotes when pasting.
>     *   Example: `C:\Users\Name\Documents\report.pdf`
> *   **macOS**: Select the file, press `Option + Command + C` to copy the path.
>     *   Example: `/Users/name/Documents/report.pdf`

### 1. `extract_pdf_content`
Core tool for extracting PDF content.

**Parameters:**
*   `file_path` (Required): Absolute path of the PDF file.
    *   *Windows Example*: `D:\Documents\paper.pdf`
    *   *macOS Example*: `/Users/username/Documents/paper.pdf`
*   `page_range` (Optional): Page range, default is "1".
    *   Examples: `"1"`, `"1-5"`, `"1,3,5"`, `"all"`.
*   `keyword` (Optional): Keyword search. If provided, ignores page range and extracts only pages containing the keyword.
*   `format` (Optional): Output format.
    *   `"text"` (Default): Plain text extraction.
    *   `"markdown"`: **Recommended**. Smartly identifies headers and paragraphs, suitable for LLM reading.

### 2. `get_pdf_metadata`
Quickly retrieves PDF metadata and Table of Contents (TOC).

**Parameters:**
*   `file_path` (Required): Absolute path of the PDF file.

### 3. `convert_markdown_to_docx`
Converts Markdown content to a Word document.

**Parameters:**
*   `markdown_content` (Required): Markdown text content to convert.
*   `output_path` (Required): Absolute path for the output .docx file.
    *   *Note*: Enter the **new file path you want to save to**.
    *   *Example*: `D:\Documents\report_output.docx`

### 4. `convert_docx_to_pdf`
Converts a Word document to a PDF file.

**Parameters:**
*   `docx_path` (Required): Absolute path of the input .docx file.
*   `pdf_path` (Optional): Absolute path for the output .pdf file.
    *   If not provided, generates a pdf file with the same name in the same directory as the original docx file.

## 📂 Output Directory Structure

After running the tool, images will be saved in the following structure:

```
extracted_images/
  └── PDF_Filename/
      ├── page_1_img_1.jpeg
      ├── page_1_img_2.png
      └── ...
```

## 💻 Development

*   **Core Code**: `src/simple_pdf/server.py`
*   **Conversion Logic**: `src/simple_pdf/convert.py`
