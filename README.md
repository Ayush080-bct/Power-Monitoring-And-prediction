## Power Monitoring & Prediction

An end-to-end demo project for **monitoring transformer load** (from incoming sensor/household current readings) and **forecasting short‑term risk**.  
It includes:

- a **FastAPI backend** that ingests readings, aggregates them into features, and predicts the next load
- a **React (Vite) frontend** dashboard that polls the backend and visualizes the latest load and risk level

---

## What it does

- Collects current readings (e.g., per house/feeder) over time
- Builds aggregated “layer2” features from raw (“layer1”) readings
- Predicts the **next total current** using a saved ML model
- Converts predicted/current load into a **risk level** based on transformer limits
- Displays live/latest values and recent history in a web dashboard

---

## How it works (high level)

### 1) Data ingestion (Layer 1: raw)
Clients send readings to the backend:

- **POST `/ingest`**
  - Body: `{ timestamp, house_id, current }`

The backend stores data in CSV (see Notes).

### 2) Aggregation (Layer 2: derived features)
The backend aggregates raw readings into “layer2” features (computed on demand) and exposes:

- **GET `/layer2/latest`** — most recent aggregated point
- **GET `/layer2/history?limit=50`** — recent aggregated history

### 3) Prediction
A prediction endpoint uses a trained model to forecast the **next total current**:

- **GET `/predict/latest`**

The model is loaded from `backend/ml/model.pkl` (see Notes). You can retrain it with `backend/ml/train_model.py`.

### 4) Risk calculation
Risk levels are computed based on transformer operating limits (defined in `backend/main.py`).  
The frontend polls the backend to show **load** and **risk**.

---

## Project structure

- `backend/` — FastAPI service, CSV storage, ML scripts/model
- `frontend/` — Vite + React dashboard UI

---

## Tools / Technologies used

### Backend
- **Python**
- **FastAPI** (web API)
- **Uvicorn** (ASGI server)
- **CSV-based storage** for readings/aggregates
- **Machine Learning** training script + serialized model (`.pkl`) (commonly via scikit-learn, depending on `requirements.txt`)

### Frontend
- **Node.js + npm**
- **Vite**
- **React**
- HTTP polling of backend endpoints for live updates

---

## Setup & Run

### Backend

1) Create a virtual environment and install dependencies:

```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

2) (Optional) Train the model:

```bash
python ml/train_model.py
```

3) Run the API:

```bash
uvicorn main:app --reload --port 8000
```

### Frontend

1) Install dependencies:

```bash
cd frontend
npm install
```

2) Run dev server:

```bash
npm run dev
```

Open: http://localhost:5173

---

## API endpoints

- `POST /ingest`
  - Body: `{ timestamp, house_id, current }`
- `GET /layer2/latest`
- `GET /layer2/history?limit=50`
- `GET /predict/latest`

---

## Notes

- Raw data is stored at: `backend/layer1_raw.csv`
- Aggregation is computed on demand into “layer2”.
- The prediction endpoint uses a model stored at: `backend/ml/model.pkl`
- Risk levels are based on transformer limits defined in: `backend/main.py`
