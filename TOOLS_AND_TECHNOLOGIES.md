# ShaadiSahulat — Tools & Technologies Specification

An Integrated Wedding Budget Planning & E-Commerce Marketplace Platform (FYP).

## Group Members
- Ahmad Raza 22F-3325 (Team Lead + Developer)
- Rafay Ali 22F-3619 (Team Lead + Backend Developer)

---

## Executive Summary

ShaadiSahulat is built on a modern, decoupled microservice and Single-Page Application (SPA) architecture. The platform combines high-throughput asynchronous web APIs, real-time bidirectional WebSockets, and deep learning computer vision microservices.

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        FRONTEND CLIENT LAYER                           │
│     React 18  •  Vite  •  Tailwind CSS  •  Socket.IO Client  •  Axios  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ HTTP REST / WebSocket Protocol
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                       BACKEND API GATEWAY LAYER                        │
│    Node.js  •  Express  •  MongoDB (Mongoose)  •  JWT  •  Tesseract.js │
└───────────────────┬────────────────────────────────┬───────────────────┘
                    │                                │
                    ▼                                ▼
┌──────────────────────────────────────┐ ┌───────────────────────────────┐
│        DOWRY ML MICROSERVICE         │ │      VISUAL AI MICROSERVICE   │
│  Python  •  Flask  •  Scikit-Learn   │ │  Python  •  PyTorch  •  Flask │
│  K-Means Clustering (Port 5001)      │ │  EfficientNet-B0 (Port 5002)  │
└──────────────────────────────────────┘ └───────────────────────────────┘
```

---

## 1. Programming Languages & Core Runtimes

| Technology | Version | Purpose in Project |
| :--- | :--- | :--- |
| **JavaScript (ES6+)** | Node.js v18+ | Primary language for both frontend React client and Express backend API Gateway. |
| **Python** | 3.10+ | Powers the two dedicated AI/ML microservices (`ml-service` and `visual-ml-service`). |
| **HTML5 & CSS3** | Modern Standards | Responsive page markup, semantic layout, and modern styling tokens. |

---

## 2. Frontend Stack (Client Layer)

| Tool / Library | Role & Functionality | Justification & Usage in Codebase |
| :--- | :--- | :--- |
| **React 18** | Single-Page Application (SPA) Framework | Provides reactive, component-based user interfaces with modular state isolation for Buyers, Sellers, Bankers, and Admins. |
| **Vite** | Build Tool & Bundler | Extremely fast Hot Module Replacement (HMR) and optimized ES-module production bundling. |
| **Tailwind CSS** | Utility-First Styling Framework | Enables rapid, responsive UI development with a custom bridal luxury aesthetic theme palette. |
| **Axios** | HTTP Client | Standardized HTTP requests with automated Bearer JWT header injection and interceptors. |
| **Socket.IO Client** | WebSocket Client | Listens to live WebSocket events emitted by the server (dispute chat, shipment tracking updates, order alerts). |
| **Lucide React** | Iconography | High-performance SVG icons for e-commerce dashboards, order badges, and status cards. |
| **Canvas Confetti** | Animation Utility | Renders celebration animations upon successful order placement and BNPL approval. |

---

## 3. Backend Stack (API Gateway Layer)

| Tool / Library | Role & Functionality | Justification & Usage in Codebase |
| :--- | :--- | :--- |
| **Node.js** | Server Runtime Environment | Event-driven, non-blocking asynchronous I/O runtime capable of handling concurrent buyer traffic. |
| **Express.js** | Web Framework | Lightweight REST API routing (`routes/`), request controllers (`controllers/`), and middleware pipeline. |
| **MongoDB & Mongoose** | NoSQL Database & ORM | Flexible document-oriented database for schemas with nested arrays (order packages, BNPL offer schedules, reviews). |
| **JSON Web Tokens (JWT)** | Authentication Token Standard | Stateless, cryptographically signed Bearer tokens (`lib/tokens.js`) storing user ID and role claims. |
| **bcryptjs** | Password Security | Multi-round salted hashing (`lib/passwords.js`) preventing plain-text credential leaks. |
| **Socket.IO** | Bidirectional WebSocket Engine | Real-time communication server (`lib/socket.js`) managing user-specific and dispute-specific live chat rooms. |
| **Tesseract.js** | Optical Character Recognition | Pure JavaScript OCR engine (`lib/ocr.js`) extracting 13-digit Pakistani CNIC numbers from uploaded images without native dependencies. |
| **pdfjs-dist & @napi-rs/canvas** | PDF Pre-processing Engine | Converts multi-page PDF documents into high-resolution 2.0x PNG buffers before OCR ingestion. |
| **Nodemailer** | SMTP Email Dispatcher | Sends automated transactional emails (`lib/mailer.js`) for 6-digit registration OTPs and order invoices. |
| **Multer** | Multipart Form-Data Parser | Manages file streams for product photos and private CNIC document uploads (`lib/storage.js`). |

---

## 4. Artificial Intelligence & Machine Learning Stack

| Technology | Role | Implementation in Project |
| :--- | :--- | :--- |
| **PyTorch (`torch`)** | Deep Learning Framework | Executes GPU/CPU tensor calculations and forward inference on convolutional neural networks (`visual-ml-service/predictor.py`). |
| **Torchvision** | Computer Vision Models | Provides the pre-trained **EfficientNet-B0** backbone and image transformation pipelines (`transforms.Resize`, `Normalize`). |
| **Scikit-Learn (`sklearn`)** | Machine Learning Toolkit | - Unsupervised **K-Means Clustering** (`ml-service/kmeans_trainer.py`) for dowry demographic budgeting.<br>- **StandardScaler** for feature normalization.<br>- **TfidfVectorizer** for bridal text vocabulary scoring. |
| **Pillow (PIL)** | Image Processing Engine | Decodes raw byte streams into RGB format, verifies image quality thresholds, and resizes query inputs. |
| **NumPy** | Numerical Computing | Vector cosine distance computations, array slicing, and color histogram variance calculations. |
| **Pickle (`.pkl`)** | Object Serialization | Serializes fitted TF-IDF vectorizers (`data/tfidf_vectorizer.pkl`) and ML models for sub-millisecond RAM loading on server startup. |
| **Flask & Flask-CORS** | Python Microservice Framework | Lightweight HTTP microservice servers exposing internal REST endpoints on **Port 5001** and **Port 5002**. |

---

## 5. Third-Party APIs & Cloud Services

| Service | Integration | Purpose & Benefits |
| :--- | :--- | :--- |
| **OpenStreetMap Nominatim** | `https://nominatim.openstreetmap.org/search` | - Free, open-source address autocomplete at checkout (`countrycodes=pk`).<br>- Provides latitude and longitude coordinates for calculating shipping charges. |
| **Cloudinary CDN** | Cloud Media Storage SDK (`lib/cloudinary.js`) | Offloads product and avatar image hosting, automatic responsive scaling, and CDN edge delivery. |
| **Google Colab (NVIDIA T4)** | Cloud GPU Training Environment | Used to fine-tune EfficientNet-B0 on custom bridal dress datasets using Cross-Entropy and Triplet Margin Loss. |
| **SMTP Mail Server** | Gmail / SendGrid Transport | Dispatches automated 6-digit registration OTP codes and order receipts. |

---

## 6. Project Management & DevOps Tools

| Tool | Application & Workflow |
| :--- | :--- |
| **Git & GitHub** | Distributed Version Control System managing pull requests, branch protection, and collaborative code reviews. |
| **Jira (Atlassian)** | Agile Scrum project management tracking User Stories, Sprint Backlogs, Epics, and Task burn-down charts. |
| **Vercel** | Serverless deployment platform configuration for production frontend deployment (`vercel.json`). |
| **Postman** | API development tool used to design, document, and test HTTP endpoints across Node.js and Flask services. |
| **Concurrently** | NPM utility running the Vite frontend and Express backend concurrently with unified colored logging via `npm run dev`. |

---

## 7. Technology Selection Justifications

### Why Node.js & Express over Next.js Serverless?
- **Long-Lived Real-Time WebSockets**: Next.js serverless functions spin down when inactive, breaking persistent WebSocket connections. Our platform requires dedicated, stateful Socket.IO connections (`lib/socket.js`) for dispute live chat and instant shipment alerts.
- **Continuous Background Timers**: ShaadiSahulat runs automatic cron timers (`lib/disputeTimer.js` and `lib/bnplTimer.js`) every 5 minutes to auto-expire unhandled applications and enforce 48-hour dispute SLAs.

### Why Python for ML instead of Pure JavaScript?
- **Scientific Computing Ecosystem**: PyTorch and Scikit-Learn provide highly optimized C++/CUDA accelerated vector search and clustering routines that have no equal in native JavaScript environments.
- **Decoupled Gateway Pattern**: The Node.js API Gateway safely handles client web traffic and offloads heavy AI inference to Python microservices on dedicated ports (5001 and 5002).
