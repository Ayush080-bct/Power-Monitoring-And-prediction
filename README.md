## Power Management Dashboard

End-to-end demo for monitoring transformer load and forecasting short-term risk.
The backend ingests sensor readings, aggregates them into layer2 features, and
predicts the next total current. The frontend polls the API to render the latest
load and risk level.

## Project Structure

- backend/: FastAPI service, CSV data, and ML scripts
- frontend/: Vite + React dashboard

## Backend

### Setup

1) Create a virtual environment and install dependencies.

```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

2) (Optional) Train the model.

```bash
python ml/train_model.py
```

### Run

```bash
uvicorn main:app --reload --port 8000
```

### API Endpoints

- POST /ingest
	- Body: { timestamp, house_id, current }
- GET /layer2/latest
- GET /layer2/history?limit=50
- GET /predict/latest

## Frontend

### Setup

```bash
cd frontend
npm install
```

### Run

```bash
npm run dev
```

Open http://localhost:5173

## Notes

- Data is stored as CSV in backend/layer1_raw.csv and aggregated on demand.
- The prediction endpoint uses a model stored at backend/ml/model.pkl.
- Risk levels are based on transformer limits defined in backend/main.py.
