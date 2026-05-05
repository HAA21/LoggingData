# Customer Data Logger

A mobile-friendly web application for logging customer entries and storing them in monthly Excel files.

Built with:
- FastAPI (backend)
- Tailwind CSS (frontend)
- openpyxl (Excel read/write)
- Vercel Blob (production storage)

## Features

- Date, customer name, quantity, and multiple phone number entry
- Monthly Excel files (`april_2025.xlsx`, etc.)
- Auto-suggest customer names from all existing monthly files
- Download monthly file via API
- Last 5 entries for selected/current month
- Responsive UI optimized for mobile
- Light/dark mode toggle

## Project Structure

```text
.
├── main.py
├── requirements.txt
├── vercel.json
├── README.md
├── static/
│   └── index.html
└── data/                  # created automatically in local mode
```

## Data Format

Each row in Excel contains:
- Date (`YYYY-MM-DD`)
- Customer Name
- Quantity (integer)
- Phone Numbers (comma-separated in one cell)

Excel headers are:
- `Date`
- `Customer Name`
- `Quantity`
- `Phone Numbers`

## Run Locally

### 1) Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 2) Install dependencies

```powershell
pip install -r requirements.txt
```

### 3) Start the app

```powershell
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Open:
- [http://localhost:8000](http://localhost:8000)

## API Endpoints

- `GET /`  
  Returns the frontend form.

- `GET /api/customers`  
  Returns unique customer names:
  ```json
  { "customers": ["Acme Corp", "Beta Ltd"] }
  ```

- `POST /api/entries`  
  Request body:
  ```json
  {
    "date": "2025-04-05",
    "customer_name": "Acme Corp",
    "quantity": 10,
    "phone_numbers": ["+1 111 222", "+1 333 444"]
  }
  ```

- `GET /api/download/{month}_{year}`  
  Example: `/api/download/april_2025`  
  Downloads the corresponding `.xlsx` file.

- `GET /api/entries/recent?month=April&year=2025`  
  Returns last 5 entries for that month.

## Storage Behavior

The app supports two modes:

- **Local mode (default in local dev):**
  - Stores files under `./data/`

- **Vercel Blob mode (production):**
  - Uses Vercel Blob API with `BLOB_READ_WRITE_TOKEN`
  - Files are stored with prefix `data/`

For low-traffic usage, writes use simple last-write-wins behavior.

## Deploy to Vercel

### 1) Push project to a Git provider

Push this folder to GitHub/GitLab/Bitbucket.

### 2) Import project in Vercel

In Vercel dashboard, create a new project from your repository.

### 3) Add environment variable

In **Project Settings -> Environment Variables**:

- `BLOB_READ_WRITE_TOKEN` = your Vercel Blob read/write token

### 4) Deploy

`vercel.json` is already configured to route requests to `main.py`.

## Notes

- `customer_name` supports special characters.
- If no files exist, the first submission creates the month file automatically.
- Dates are validated and stored in `YYYY-MM-DD`.
