# System Requirements & Software Design Document (SRDD)
**Project Name:** Client-Side PDF & Image Assembler  
**Version:** 1.0.0  
**Status:** Draft / Approved  

---

## 1. Requirements Specification

### 1.1 Scope & Purpose
The **Client-Side PDF & Image Assembler** is a web utility that enables users to merge, reorder, filter, and format pages from PDF documents and image files into a single consolidated PDF document. 

All file parsing, page manipulation, canvas rendering, and PDF generation occur **100% client-side** within the user's web browser. No files, documents, or personal data are transmitted to external servers.

---

### 1.2 Functional Requirements (FR)

* **FR-1: File Ingestion**
  * **FR-1.1:** System shall accept files via drag-and-drop or local file selector.
  * **FR-1.2:** System shall support PDF (`.pdf`) and common image formats (`.png`, `.jpg`, `.jpeg`, `.webp`).
  * **FR-1.3:** System shall support loading multiple files simultaneously.

* **FR-2: Page Filtering & Extraction**
  * **FR-2.1:** System shall parse PDF documents to extract total page count and individual page structures.
  * **FR-2.2:** System shall allow page sequence inputs per file (e.g., `1, 3-5, 8`).
  * **FR-2.3:** Updating the range input shall immediately update the global page sequence.

* **FR-3: Cross-File Page Reordering**
  * **FR-3.1:** System shall compile a global page tray representing the final document order across all loaded files.
  * **FR-3.2:** System shall provide HTML5 drag-and-drop reordering for individual page thumbnails.
  * **FR-3.3:** System shall maintain absolute page index indicators (e.g., `#1`, `#2`).

* **FR-4: Standardized A4 Layout Engine for Images**
  * **FR-4.1:** Image files shall be placed on standard A4 PDF pages ($595.28 \times 841.89$ points) rather than using raw image dimensions.
  * **FR-4.2:** Images shall be scaled proportionally using a uniform scale factor:
    $$\text{scale} = \min\left(\frac{W_{\text{max}}}{W_{\text{img}}}, \frac{H_{\text{max}}}{H_{\text{img}}}, 1.0\right)$$
  * **FR-4.3:** System shall enforce a minimum margin ($36\text{ pt} = 0.5\text{ in}$) around embedded images.
  * **FR-4.4:** Images shall be centered both vertically and horizontally within the printable page area.

* **FR-5: Preview & Document Export**
  * **FR-5.1:** System shall render thumbnail previews for every page using dynamic canvas rendering.
  * **FR-5.2:** System shall allow modal previewing of individual pages or the full combined PDF in an `<iframe>`.
  * **FR-5.3:** System shall export the final assembly as a downloadable `.pdf` file.

---

### 1.3 Non-Functional Requirements (NFR)

* **NFR-1: Privacy & Data Security:** No data, metadata, or document streams shall leave the browser environment.
* **NFR-2: Performance:** Rendering of individual page thumbnails shall complete in $\le 300\text{ms}$ per page. PDF assembly shall complete within $3\text{ seconds}$ for documents under 50 pages.
* **NFR-3: Usability:** Interface must present responsive feedback during file processing, loading states, and drag operations.
* **NFR-4: Portability:** Application must run seamlessly across all modern Chromium, WebKit, and Gecko browsers without backend dependencies or server setup.

---

## 2. Software Design & Architecture

### 2.1 System Architecture Diagram

+ 
                  ---------------------------------------  
                 |             USER BROWSER              |  
                 |                                       |
                 |  +---------------------------------+  |
                 |  |          HTML5 / CSS3 UI        |  |
                 |  +---------------+-----------------+  |
                 |                  |                    |
                 |  +---------------+-----------------+  |
                 |  |     Application Logic (JS)      |  |
                 |  +-------+-----------------+-------+  |
                 |          |                 |          |
                 |          v                 v          |
                 |  +---------------+  +--------------+  |
                 |  |   pdfjs-dist  |  |   pdf-lib    |  |
                 |  | (Page Render) |  | (PDF Create) |  |
                 |  +---------------+  +--------------+  |
                 +---------------------------------------+
                                    |
                    [ Zero Network Transmission ]


---

### 2.2 Core Data Structures

```js
// Loaded File Object Structure
interface LoadedFile {
  id: string;              // Unique file identifier (e.g., 'file-16982...').
  file: File;              // Raw Browser File reference.
  name: string;            // Original file name.
  type: 'pdf' | 'image';   // Identified file type category.
  pageCount: number;       // Total number of pages.
  pdfDoc?: PDFDocumentProxy; // pdf.js document reference (PDFs only).
}

// Global Reorder Sequence Entry
interface SequenceItem {
  seqId: string;           // Unique item identifier in global tray.
  fileId: string;          // Maps back to LoadedFile.id.
  pageNum: number;         // 1-based index of source page.
}
```


2.4 Image-to-A4 Layout Algorithm  
+ 
-------------------------------------------------------------  
|                      A4 PAGE BOUNDS                         |  
|  (595.28 pt x 841.89 pt)                                    |
|                                                             |
|  +-------------------------------------------------------+  |
|  |                 PRINTABLE BOUNDS                      |  |
|  |  Margin = 36 pt                                       |  |
|  |                                                       |  |
|  |  +-------------------------------------------------+  |  |
|  |  |                                                 |  |  |
|  |  |                SCALED IMAGE                     |  |  |
|  |  |            (Aspect Ratio Preserved)             |  |  |
|  |  |                                                 |  |  |
|  |  +-------------------------------------------------+  |  |
|  |                                                       |  |
|  +-------------------------------------------------------+  |
|                                                             |
+-------------------------------------------------------------+


### 2.5 Security & Privacy Design Verification  
1. Zero External Requests: Application runtime does not execute fetch(), XMLHttpRequest, or WebSocket calls targeting document payloads.

2. Local Memory Isolation: Blob URLs created via URL.createObjectURL() point exclusively to client memory addresses (blob:http...) and are cleaned up upon file dismissal.

3. Sandbox Compliance: Generates output documents purely in JS heap buffers prior to triggering browser DOM downloads.