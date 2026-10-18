# ShaadiSahulat

An Integrated Wedding Budget Planning & E-Commerce Marketplace Platform (FYP).

## Purpose
Helps Pakistani families plan category-wise wedding budgets using a 
clustering-based estimation model, and purchase items directly against that 
budget through Retail and Thrift storefronts — with COD and BNPL financing.

## Group Members
- Ahmad Raza 22F-3325  (Team Lead + Developer)
- Rafay Ali 22F-3619  (Team Lead + backend Developer)

## Technologies
- Frontend: React
- Backend: Node.js / Express
- Database: MongoDB
- ML Service: Python (clustering-based estimation)
- Project Management: Jira (Scrum) + GitHub

## Project Management
Jira project: [paste your Jira project URL here]

---

# Dowry Budget ML Microservice Documentation

The Dowry Budget ML Microservice is a lightweight Python Flask service running on **Port 5001**. It works in tandem with the Node.js rule engine to provide safe, realistic, category-wise wedding budget estimations using unsupervised machine learning clustering.

---

## 2-Layer Hybrid Estimation Architecture

```text
[User Financial & Family Demographics]
                  │
                  ▼
┌────────────────────────────────────────────────────────┐
│  Layer 1: Rule-Based Logic Engine (services/ruleEngine.js)
│  - Financial Safety Caps (40% Income, 80% Savings)     │
│  - Responsibility Score (Unmarried Children & Age)     │
│  - Category Base Allocation (Dress 45%, Furniture 20%) │
│  - Grounded with Database Average Top-5 Item Prices    │
└─────────────────────────┬──────────────────────────────┘
                          │ Baseline Budget
                          ▼
┌────────────────────────────────────────────────────────┐
│  Layer 2: K-Means Clustering ML (ml-service/ Port 5001)│
│  - Trained on Income, Savings, Dependents, Youngest Age│
│  - Predicts ml_adjustment_factor (e.g. +8.5% or -5.0%) │
└─────────────────────────┬──────────────────────────────┘
                          │
                          ▼
[Final Recommended Budget & Category Breakdown Persisted in MongoDB]
```

---

## Core Formulas & Rules

1. **Financial Boundary Caps**:
   $$\text{Max from Income} = \text{Monthly Income} \times 12 \times 0.40 \quad (\text{40\% annual income})$$
   $$\text{Max from Savings} = \text{Available Savings} \times 0.80 \quad (\text{80\% total savings})$$
   $$\text{Base Pool} = \min(\text{Max from Income}, \text{Max from Savings}) + \text{Contribution}$$

2. **Responsibility Score Adjustment**:
   $$S = 1.0 - \text{Unmarried Reduction} - \text{Age Reduction}$$
   - **Multiple Unmarried Children**: Reduction = $0.15 \times (\text{Unmarried Children Count} - 1)$
   - **Youngest Child Age**:
     - $< 18$ years: $-0.10$ (10% reduction for future educational/raising costs)
     - $18–22$ years: $-0.05$ (5% reduction for near-term marriage costs)
     - $> 22$ years: $0.00$
   - Score is clamped to a minimum floor of $0.50$ (50%).

3. **Baseline Budget**:
   $$\text{Baseline Budget} = \lfloor \text{Base Pool} \times \text{Responsibility Score} \rfloor$$

4. **Category Base Weights & Priority Multipliers**:
   - `wedding_dress`: 45%
   - `furniture`: 20%
   - `electronics`: 15%
   - `kitchen_items`: 12%
   - `decoration`: 5%
   - `miscellaneous`: 3%
   - Priority multipliers: `High` (1.20), `Medium` (1.00), `Low` (0.80), `Not_Wanted` (0.00).

5. **Market Price Grounding**:
   $$\text{Grounded Amount} = 0.60 \times \text{Rule Amount} + 0.40 \times \text{DB Average Top-5 Item Prices}$$

6. **K-Means ML Adjustment**:
   $$\text{Final Budget} = \lfloor \text{Baseline Budget} \times (1 + \text{ml\_adjustment\_factor}) \rfloor$$

---

## Directory Structure

```text
ml-service/
├── app.py                         # Flask microservice entry point & REST endpoints
├── dataset_generator.py           # Generates synthetic demographic records for training
├── kmeans_trainer.py              # Scikit-learn K-Means model training & scaling pipeline
├── predictor.py                   # Predicts ml_adjustment_factor for incoming user data
├── requirements.txt               # Python package dependencies
├── data/                          # Dataset storage directory
│   └── dowry_synthetic_dataset.csv# Pre-generated training dataset
└── models/                        # Serialized model artifacts
    ├── kmeans_model.pkl           # Trained Scikit-learn KMeans clustering model
    ├── scaler.pkl                 # Fitted StandardScaler object
    └── cluster_stats.json         # Cluster demographic statistics & mean adjustment factors
```

---

## API Endpoints

- **`GET /health`**: Health check probe returning service status.
- **`POST /ml/dowry-adjustment`**:
  - Request body: `{ "income": 80000, "savings": 500000, "unmarried_children": 2, "youngest_age": 16 }`
  - Response: `{ "success": true, "adjustment_factor": 0.08, "cluster_id": 2, "similar_users_count": 45 }`
- **`POST /ml/train`**: Trains the K-Means clustering model on `data/dowry_synthetic_dataset.csv`.
- **`POST /ml/add-user`**: Appends real completed user budgets to the dataset and triggers model retraining.
- **`POST /ml/generate-dataset`**: Generates a new synthetic training dataset.

---

## How to Run the ML Service

```bash
cd ml-service

# 1. Create and activate a Python virtual environment
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
# source venv/bin/activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Start Flask service (default port: 5001)
python app.py
```
