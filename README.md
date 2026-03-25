# ai-risk-engine-python

FastAPI-based risk scoring engine with deterministic logic and a clean `POST /analyze` endpoint.

## What It Does

Accepts supplier or deal data via a POST request, runs deterministic risk scoring across multiple dimensions, and returns a structured JSON risk report. No AI dependency — pure logic-based scoring.

## Stack

- Python 3.11+
- FastAPI
- Pydantic (input validation)
- Uvicorn (ASGI server)

## Install

```bash
pip install fastapi uvicorn pydantic
```

## Run

```bash
uvicorn main:app --reload
```

Server runs at http://localhost:8000

## Endpoint

### POST /analyze

**Request body:**

```json
{
  "supplier_name": "Shenzhen TechParts Co.",
  "unit_price": 4.20,
  "moq": 500,
  "lead_time_days": 35,
  "category": "electronics",
  "years_in_business": 1
}
```

**Response:**

```json
{
  "risk_score": 72,
  "risk_level": "High",
  "flags": ["new_supplier", "high_risk_category", "long_lead_time"],
  "breakdown": {
    "price_risk": 15,
    "lead_time_risk": 20,
    "category_risk": 25,
    "supplier_age_risk": 12
  },
  "recommendation": "Request verified samples and trade references before committing to order."
}
```

## Scoring Logic

Risk is calculated deterministically — no AI, no randomness:

- **Price risk**: flags if unit price is below category baseline (potential quality/fraud signal)
- **Lead time risk**: scores increase above 21 days, max penalty at 60+ days
- **Category risk**: electronics, supplements, and apparel carry higher baseline risk
- **Supplier age risk**: businesses under 2 years old receive a risk premium

Final score is the weighted sum of all dimensions (0–100). Scores above 60 = High Risk.

## Project Structure

```
ai-risk-engine-python/
├── main.py          # FastAPI app + POST /analyze endpoint
├── scoring.py       # Deterministic scoring logic
├── models.py        # Pydantic request/response models
└── README.md
```
