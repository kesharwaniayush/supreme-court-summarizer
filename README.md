# Indian Supreme Court Judgment Summarizer

A web-based application for extractive summarization of Indian Supreme Court judgments. The system is being developed to help users process lengthy legal documents and obtain concise summaries containing important sentences from the original judgment.

> **Project status:** Frontend and backend foundations are implemented and tested. The summarization model is under development by a separate team member. Final model integration and live deployment are pending.

## Table of Contents

- [Project Overview](#project-overview)
- [Objectives](#objectives)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Implemented Features](#implemented-features)
- [Backend API Endpoints](#backend-api-endpoints)
- [Installation and Setup](#installation-and-setup)
- [Testing](#testing)
- [Current Limitations](#current-limitations)
- [Pending Work](#pending-work)
- [Deployment Plan](#deployment-plan)
- [Research Dataset and Methodology](#research-dataset-and-methodology)

## Project Overview

Indian Supreme Court judgments are often lengthy and contain complex legal language. Reading and reviewing these documents can be time-consuming.

This project aims to develop a web application that accepts legal judgments in PDF, DOCX, or TXT format, or as pasted text, and generates an extractive summary using a dedicated summarization model.

Unlike abstractive summarization, extractive summarization selects important sentences from the original document rather than generating entirely new sentences.

The application is being developed as a final-year academic project using a React frontend and a FastAPI backend.

## Objectives

- Provide a simple interface for submitting legal judgments.
- Support PDF, DOCX, and TXT document formats.
- Extract readable text from uploaded documents.
- Integrate an extractive summarization model developed separately.
- Display the generated summary and basic document statistics.
- Allow users to download the summary as a text file.
- Provide appropriate input validation and error handling.
- Evaluate the summarization system using the project's research methodology.

## Technology Stack

| Component | Technology |
|---|---|
| Frontend | React |
| Development and build tool | Vite |
| Programming language | TypeScript |
| Styling | CSS |
| Icons | Lucide React |
| Backend | Python, FastAPI |
| API server | Uvicorn |
| PDF extraction | pypdf |
| DOCX extraction | python-docx |
| Request and file handling | python-multipart |
| Automated testing | pytest, HTTPX |
| Planned frontend hosting | Vercel |
| Planned backend hosting | Render |

## Project Structure

```text
supreme-court-summarizer/
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   │   └── SummaryResults.tsx
│   │   ├── services/
│   │   │   └── summarizerApi.ts
│   │   ├── types/
│   │   │   └── summary.ts
│   │   ├── App.tsx
│   │   ├── App.css
│   │   └── ...
│   ├── .env.example
│   ├── .gitignore
│   ├── package.json
│   └── ...
│
├── backend/
│   ├── tests/
│   │   └── test_main.py
│   ├── main.py
│   ├── requirements.txt
│   └── ...
│
└── README.md
```

The structure above represents the current intended organization. Some configuration files and directories may vary as development progresses.

## Implemented Features

### Frontend

- Minimal, academic-style user interface.
- Upload interface for PDF, DOCX, and TXT documents.
- Drag-and-drop file upload.
- Option to paste judgment text directly.
- File extension validation.
- Maximum file size validation of 10 MB.
- Word-count display for entered text.
- Loading and error states.
- API service for communicating with the backend.
- Summary results component with summary statistics.
- Download functionality for summaries in `.txt` format.
- Responsive styling and basic user feedback.

### Backend

- FastAPI application initialized with project metadata.
- API health and status endpoints.
- PDF, DOCX, and TXT text extraction.
- Validation of supported file types.
- File-size validation with a 10 MB limit.
- Handling for empty or unreadable documents.
- Handling for password-protected PDFs.
- Clear error responses for invalid input.
- CORS configuration for frontend-backend communication.
- Environment-variable-based configuration for allowed frontend origins.
- Placeholder for integrating the extractive summarization model.

### Frontend–Backend Integration

The frontend communicates with the FastAPI backend using HTTP requests and multipart form data.

The backend has been tested locally, and the frontend successfully reaches the summarization endpoint. The current HTTP 501 response is intentional because the actual summarization model has not yet been integrated.

## Backend API Endpoints

The backend currently exposes the following endpoints.

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/` | Returns API information and running status |
| GET | `/health` | Returns backend health and model connection status |
| POST | `/api/extract-text` | Extracts readable text from an uploaded document |
| POST | `/api/summarize` | Validates the input and reserves the integration point for summarization |

### Health Check

Request:

```http
GET /health
```

Current response:

```json
{
  "status": "healthy",
  "model_connected": false
}
```

The `model_connected` value is currently `false` because the summarization model is not integrated.

### Text Extraction

Request:

```http
POST /api/extract-text
```

The request accepts a document through a multipart form field named `file`.

For valid documents, the endpoint returns the filename, extracted text, and word count.

### Summarization

Request:

```http
POST /api/summarize
```

The endpoint accepts either an uploaded file or pasted text. It validates the input and extracts the judgment text when necessary.

**Current behavior:** The endpoint returns HTTP `501 Not Implemented` with the message:

`Document text was accepted, but the summarization model has not been integrated yet.`

This is an expected temporary behavior, not a completed summarization feature.

## Installation and Setup

### Prerequisites

Install the following before running the application:

- Python 3.10 or a compatible Python version.
- Node.js and npm.
- Git (for version control and repository upload).

### 1. Clone the Repository

Once the project has been uploaded to GitHub:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd supreme-court-summarizer
```

Replace the placeholder with the actual repository URL.

### 2. Set Up the Backend

Open a terminal in the project root.

On Windows PowerShell:

```powershell
cd backend

python -m venv .venv
.\.venv\Scripts\Activate.ps1

python -m pip install -r requirements.txt
```

Start the development server:

```powershell
python -m uvicorn main:app --reload --port 8000
```

The backend should be available at:

- API root: `http://127.0.0.1:8000/`
- Health check: `http://127.0.0.1:8000/health`
- Interactive API documentation: `http://127.0.0.1:8000/docs`

### 3. Set Up the Frontend

Open a separate terminal:

```powershell
cd frontend
npm install
```

Create a `.env` file in the `frontend/` directory:

```dotenv
VITE_API_BASE_URL=http://localhost:8000
```

Start the frontend development server:

```powershell
npm run dev
```

Open the URL printed by Vite, normally:

`http://localhost:5173/`

Keep the backend and frontend servers running in their respective terminals.

**Important:** The frontend environment variable is included in the client-side build. Never place passwords, secret keys, or other private credentials in `VITE_` variables.

## Testing

Backend automated tests are located in:

`backend/tests/test_main.py`

Run them from the backend directory after activating the virtual environment:

```powershell
python -m pytest tests/ -v
```

The current test suite contains eight tests covering the existing API behavior, including:

- API root and health endpoints.
- Valid text-file extraction.
- Unsupported file types.
- Empty text files.
- Oversized files.
- The expected HTTP 501 response from the summarization endpoint.
- Empty summarization input.

**Current verification status:**
- All eight backend tests have passed in the local development environment.
- The frontend production build has completed successfully using `npm run build`.
- The frontend-backend connection has been verified locally.
- Live deployment testing has not yet been performed.

## Current Limitations

The following limitations remain until the relevant features are implemented:

1. **Summarization model:** The actual extractive summarization model is still under development by a teammate.
2. **Generated summaries:** The application cannot yet produce real judgment summaries.
3. **Model evaluation:** Final evaluation must be performed after model integration, using the project's designated evaluation methodology and test data.
4. **OCR:** Scanned PDFs without an embedded text layer are not currently supported.
5. **Production deployment:** The application has not yet been deployed to public hosting.
6. **Production API configuration:** The final hosted frontend and backend URLs still need to be configured.
7. **End-to-end verification:** The complete workflow must be tested after model integration and deployment.

## Pending Work

The remaining work is organized into the following stages.

### Stage 1: Model Integration

- Obtain the model implementation and required trained files from the teammate.
- Review model dependencies, loading requirements, and input/output format.
- Connect the model to the existing FastAPI summarization endpoint.
- Return the generated summary and the statistics required by the frontend.
- Update the health endpoint to report model integration accurately.
- Add model-specific tests without changing the agreed research methodology.

### Stage 2: Final Testing

- Test PDF, DOCX, and TXT inputs.
- Test pasted judgment text.
- Verify empty inputs, unsupported files, oversized files, and unreadable documents.
- Verify frontend error handling and summary display.
- Verify summary download and statistics.
- Test the complete workflow from document submission to result display.
- Evaluate the summarization model according to the project plan.

### Stage 3: GitHub Upload

- Review the project structure.
- Verify `.gitignore` rules.
- Ensure `.env`, virtual environments, `node_modules`, and generated build files are excluded.
- Check that no secrets or restricted files are included.
- Create the GitHub repository and push the project.

### Stage 4: Deployment

- Deploy the React frontend to Vercel.
- Deploy the FastAPI backend to Render.
- Configure the backend's `FRONTEND_ORIGINS` environment variable.
- Configure the frontend's `VITE_API_BASE_URL` with the deployed backend URL.
- Verify CORS, API health, and frontend-backend connectivity.
- Run production smoke tests and document the live URLs.

## Deployment Plan

The planned hosting architecture is:

- **Frontend:** Vercel
- **Backend:** Render
- **Version control:** GitHub

The frontend and backend will be deployed separately. The frontend will communicate with the backend using its configured API base URL.

The backend's allowed frontend origins will be configured using the `FRONTEND_ORIGINS` environment variable. In local development, the default allowed origin is `http://localhost:5173`.

Live deployment URLs will be added here after deployment is completed and verified.

## Research Dataset and Methodology

The project plan specifies the IN-Abs dataset for the Indian Supreme Court judgment summarization research.

The documented dataset plan includes 7,030 judgment–headnote pairs for development and 100 official test cases reserved for final evaluation. The designated test cases must remain untouched until the final evaluation stage.

The planned research approach includes an enhanced extractive summarization pipeline involving InLegalBERT embeddings, K-Means clustering, MLP-based sentence relevance scoring, and outcome-related sentence handling, as described in the research plan.

**Implementation status:** These research components are not yet integrated into the current web application. Their implementation and evaluation remain dependent on the model-development work.

The web application should integrate the agreed model without independently changing the research methodology or using the reserved test cases for development.

---

## Project Status Summary

| Component | Status |
|---|---|
| Frontend development | Implemented |
| Backend API foundation | Implemented |
| Document text extraction | Tested locally |
| Backend automated tests | 8 tests passing |
| Frontend production build | Successful |
| CORS configuration | Environment-variable based |
| Summarization model | In development |
| Model integration | Pending |
| Final evaluation | Pending |
| GitHub repository upload | Pending |
| Vercel deployment | Pending |
| Render deployment | Pending |

This README will be updated as the model integration, final testing, and deployment stages are completed.
