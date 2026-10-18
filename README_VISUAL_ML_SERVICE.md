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

# Visual Recommendation & Virtual Try-On ML Microservice

The Visual ML Microservice is a deep learning Python Flask service running on **Port 5002**. It provides AI-powered visual dress recommendation, multi-modal vector similarity search, and size-aware virtual try-on fit estimation.

---

## Visual Search Architecture & Pipeline

```text
[Query Dress Image Upload]
             │
             ▼
[Stage 1: Safety & Quality Gate] (Validates dimensions >= 120px and color variance)
             │
             ▼
[Stage 2: PyTorch EfficientNet-B0 Feature Extractor]
  - Resizes to 224x224 & Normalizes with ImageNet weights
  - Extracts 1280-dimensional feature embedding vector
             │
             ▼
[Stage 3: Hybrid 3-Dimensional Scoring]
  - 70% Image Vector Cosine Similarity (PyTorch)
  - 20% TF-IDF Text Vocabulary Similarity (loaded from data/tfidf_vectorizer.pkl)
  - 10% HSV Dominant Color Match
             │
             ▼
[Ranked Matching Dresses Returned to Node.js & Enriched from MongoDB]
```

---

## Key Capabilities

1. **1280-Dimensional Vector Search**:
   - Uses PyTorch **EfficientNet-B0** convolutional neural network backbone to extract 1280-dimensional numerical vectors representing dress silhouettes, fabric patterns, and embroidery textures.
   - Computes cosine similarity against indexed catalog dress embeddings:
     $$\text{Cosine Similarity} = \frac{\vec{Q} \cdot \vec{C}}{\|\vec{Q}\| \|\vec{C}\|}$$

2. **Role of `.pkl` Serialization (`data/tfidf_vectorizer.pkl`)**:
   - Serializes a pre-fitted scikit-learn `TfidfVectorizer` containing vocabulary from bridal dress descriptions.
   - Loads into RAM in **~2 milliseconds** on startup, avoiding repetitive runtime text fitting and enabling instant hybrid text-and-image scoring.

3. **Size-Aware Fit Engine (`size_fit_engine.py`)**:
   - Takes buyer body dimensions (height, weight, chest, waist) and compares them with garment sizing specifications.
   - Returns a confidence score and a fit verdict:
     - `FIT`: Measurements fall within normal wearing tolerance.
     - `TOO_SMALL`: Measurements exceed garment size boundaries.
     - `TOO_LARGE`: Garment dimensions exceed buyer measurements.

4. **Virtual Try-On Request Generator (`tryon_generator.py`)**:
   - Generates visual dress overlay rendering requests combining user photos with dress catalog images.

5. **Colab GPU Model Training**:
   - Fine-tuned on Google Colab (`colab-notebook/ShaadiSahulat_Visual_Training.ipynb`) using an NVIDIA T4 GPU.
   - Trained across 3 bridal categories (`bridal_lehenga`, `bridal_sharara`, `bridal_saree`) using Cross-Entropy classification followed by Triplet Margin Loss metric learning.
   - Weights saved as `models/fine_tuned_efficientnet_b0.pth` (19.2 MB).

---

## Directory Structure

```text
visual-ml-service/
├── app.py                         # Flask API service router & internal auth gate
├── config.py                      # Configurations, threshold limits & category definitions
├── predictor.py                   # 3-stage hybrid predictor & EfficientNet feature extractor
├── embedding_index.py             # Cosine similarity vector search index
├── tfidf_engine.py                # Text TF-IDF vectorizer & vocabulary engine
├── description_generator.py       # HSV dominant color & embroidery density analyzer
├── size_fit_engine.py             # Size evaluation & fit confidence engine
├── tryon_generator.py             # Virtual try-on rendering request builder
├── seller_routes.py               # Endpoints for indexing new seller product photos
├── tryon_routes.py                # Endpoints for virtual try-on requests
├── dowry_routes.py                # Endpoints for category price statistics
├── requirements.txt               # Python package dependencies (torch, torchvision, pillow, etc.)
├── data/                          # Data artifacts
│   ├── market_prices.json         # Category price benchmarks
│   └── tfidf_vectorizer.pkl       # Serialized scikit-learn TF-IDF vectorizer
├── models/                        # Deep learning model artifacts
│   ├── fine_tuned_efficientnet_b0.pth # Trained EfficientNet-B0 PyTorch weights (19.2 MB)
│   ├── category_classifier.pth    # Fine-tuned classification head
│   └── class_names.json           # Class label mappings
└── uploads/                       # Dress catalog image dataset organized by category
```

---

## API Endpoints

- **`GET /health`**: Health check probe returning service status.
- **`POST /visual/recommend`**: Accepts multipart dress image upload and optional text description; returns ranked matching product IDs with similarity percentages.
- **`POST /visual/tryon`**: Accepts buyer measurements and garment ID; returns fit verdict (`FIT`, `TOO_SMALL`, `TOO_LARGE`) and confidence score.
- **`POST /seller/index-product`**: Extracts 1280d embedding for a newly uploaded seller product and adds it to the search index.
- **`GET /dowry/category-prices`**: Returns average price stats for the top-5 cheapest items per category to ground the dowry budget engine.

---

## How to Run the Visual ML Service

```bash
cd visual-ml-service

# 1. Create and activate a Python virtual environment
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
# source venv/bin/activate

# 2. Install dependencies (PyTorch, torchvision, Pillow, scikit-learn)
pip install -r requirements.txt

# 3. Start Flask service (default port: 5002)
python app.py
```
