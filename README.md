# Medical-Form-OCR-automation
Privacy-focused, self-hosted medical document OCR pipeline using n8n, local Vision-Language Models (VLM), and Discord triggers for automated structured data extraction.
# Autonomous Medical Form OCR & Dynamic Data Extraction Pipeline

An automated, self-hosted document processing pipeline built with **n8n**, integrating **Discord Bot Triggers**, **PDF-to-Image Rendering**, and local **Vision-Language Models (VLM)** via OpenAI-compatible endpoints. 

Designed for low-latency, privacy-focused medical document parsing without relying on external third-party API services for optical character recognition.
Local VLM/N8N must have certain setting adjusted and different trigger should be used in order to be hipaa compliant.

---

## 🏗️ Architecture & Data Flow
![image]https://raw.githubusercontent.com/chenkyle1/Medical-Form-OCR-automation/refs/heads/main/Workflow.png

[ Discord Trigger ] ──> [ Download PDF/Image ] ──> [ Convert PDF to PNG ]
│
▼
[ Downstream Export ] <── [ Dynamic JSON Normalization ] <── [ Base64 Encoding & Local VLM Inference ]


1. **Trigger & Ingestion:** Listens for incoming `/form` attachments in targeted Discord channels.
2. **Document Conversion:** Downloads attached documents and converts multi-page PDFs to high-resolution PNG buffers.
3. **Encoding & Payload Assembly:** Converts image buffers into standardized `data:image/png;base64` Data URIs for LLM vision ingestion.
4. **Local VLM Inference:** Dispatches an HTTP POST payload to a locally hosted OpenAI-compatible inference server (e.g., LM Studio / vLLM / Ollama) running vision models such as Qwen2-VL or LLaVA.
5. **Dynamic Data Normalization:** Uses custom JS sandboxing to sanitize LLM outputs, strip markdown code fences, locate embedded JSON string blocks, and output flattened key-value key pairs.

---

## 🛠️ Key Technical Highlights

* **Privacy-Preserving OCR:** Operates entirely against local VLM endpoints (`http://localhost:1234`), keeping sensitive medical data within local network boundaries.
* **Resilient Parsing Logic:** Custom JavaScript normalization handling raw JSON, markdown-wrapped blocks (` ```json ... ``` `), and unstructured text responses.
* **Modular Integration:** Native hook points for exporting sanitized JSON directly to databases, local storage, or webhook destinations.

---

## 🚀 Quickstart & Setup

### Prerequisites

* An active **n8n** instance (Self-hosted via Docker or Desktop).
* **Local VLM Host:** LM Studio, vLLM, or Ollama exposing an OpenAI-compatible endpoint at `http://localhost:1234/v1/chat/completions`.
* **Discord Bot Token:** Configured with message read/write permissions.
* Custom community nodes installed in n8n (if applicable):
  * `n8n-nodes-pdfconvert`
  * `n8n-nodes-discord-dnd`

### Installation

1. Clone this repository:
   ```bash

    Open your n8n canvas.

    Select Workflows > Import from File and upload workflows/medical-form-ocr.json.

    link your Discord API credentials under the Discord Trigger node. (I used discord out of convenience it is not encrypted and should not be used for ephi)

    Ensure your local VLM host is running and accessible from your n8n container/host network.
