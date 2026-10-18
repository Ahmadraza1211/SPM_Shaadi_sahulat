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

# Backend Service Documentation (Node.js & Express)

The backend service serves as the core API Gateway and business logic orchestration engine for ShaadiSahulat. Built with Node.js, Express, and Mongoose, it handles authentication, multi-seller e-commerce orders, BNPL financing workflows, real-time WebSockets, OCR processing, and proxies AI requests to Python microservices.

---

## Key Responsibilities

1. **API Gateway & Routing**:
   - Central entry point on `http://localhost:5000/api`.
   - Role-based route gating (`buyer`, `seller`, `banker`, `admin`) enforcing JWT authorization.
2. **Order Management & Cart Splitting**:
   - `lib/checkoutSession.js` breaks multi-seller orders into independent seller packages under a master order ID.
   - `lib/inventory.js` executes atomic product inventory locks to prevent overselling.
3. **BNPL Lifecycle & OCR Extraction**:
   - Manages financing applications, bank officer approval workflows, and installment repayment tracking (`lib/bnplRepayment.js`).
   - Uses `lib/ocr.js` (Tesseract.js and pdfjs-dist) to extract 13-digit Pakistani CNIC numbers from uploaded images or PDF documents.
   - Stores sensitive financial verification files in encrypted private storage (`lib/privateFiles.js`).
4. **Fulfillment, Disputes & SLA Escalation**:
   - Enforces sequential package shipping states (`PENDING` -> `PROCESSING` -> `SHIPPED` -> `DELIVERED`).
   - Escrow funds release to seller payout balances upon buyer delivery confirmation.
   - 48-hour SLA countdown timer monitoring seller dispute replies (`lib/disputeSla.js`, `lib/disputeTimer.js`).
5. **Real-Time Communication**:
   - Socket.IO server (`lib/socket.js`) broadcasting live order status, notifications, and dispute chat messages.
   - Email dispatch via Nodemailer (`lib/mailer.js`).

---

## Directory Breakdown

```text
shaadi-sahulat/
├── server.js                      # Express application bootstrap & Socket.IO initialization
├── routes/                        # HTTP REST route endpoints
│   ├── auth.js                    # User registration, login, OTP & password reset
│   ├── orders.js                  # Checkout, order creation & buyer delivery tracking
│   ├── bnpl.js                    # BNPL application submission & document access
│   ├── bank.js                    # Banker approval portal & installment recording
│   ├── disputes.js                # Dispute creation, evidence upload & chat messages
│   ├── reviews.js                 # Order-gated product reviews & AI summary updates
│   ├── visual.js                  # Visual search & Virtual Try-On request proxy
│   ├── dowry.js                   # Dowry estimation gateway endpoint
│   ├── seller.js                  # Seller catalog listing & profile management
│   ├── sellerOrders.js            # Seller sub-package fulfillment & shipping updates
│   ├── admin.js & adminExt.js     # Admin dashboard & platform moderation
│   └── notifications.js           # In-app user notifications
├── controllers/                   # Request controllers & business logic
│   ├── authController.js          # Authentication logic (bcrypt, JWT token signing)
│   ├── buyerController.js         # Buyer dashboard spending calculations
│   ├── visualController.js        # Gateway proxy to Python visual ML service
│   ├── dowryController.js         # Dowry estimation orchestration & profile persistence
│   ├── disputeController.js       # Dispute ticket management & escrow freezing
│   ├── sellerController.js        # Seller management & payout withdrawal requests
│   └── registrationController.js  # User signup & OTP verification controller
├── lib/                           # Core utilities, engines & background timers
│   ├── checkoutSession.js         # Multi-seller cart splitter & pricing calculations
│   ├── inventory.js               # Atomic MongoDB product stock reservations
│   ├── ocr.js                     # Tesseract OCR CNIC extraction pipeline
│   ├── privateFiles.js            # Encrypted local private document storage
│   ├── bnplFulfillment.js         # BNPL contract creation & order fulfillment clearance
│   ├── bnplRepayment.js           # Repayment schedule & running balance engine
│   ├── bnplTimer.js               # 24-hour auto-expiration for stale BNPL applications
│   ├── disputeSla.js              # 48-hour SLA deadline calculation
│   ├── disputeTimer.js            # Background SLA deadline monitoring timer
│   ├── tokens.js                  # JWT token creation & verification
│   ├── authorize.js               # Role-based middleware enforcement
│   ├── socket.js                  # Socket.IO WebSocket room broadcaster
│   ├── mailer.js                  # Nodemailer email dispatcher
│   └── cloudinary.js              # Cloudinary media storage SDK wrapper
├── models/                        # Mongoose schemas (MongoDB collections)
│   ├── User.js & Buyer.js         # User identity & buyer profile schemas
│   ├── Seller.js                  # Seller store profile schema
│   ├── Product.js                 # Catalog product schema
│   ├── Order.js                   # Master order schema
│   ├── Package.js                 # Sub-order package schema per seller
│   ├── BnplApplication.js         # BNPL financing application schema
│   ├── BnplOfferLetter.js         # Approved BNPL terms & repayment schedule schema
│   ├── Dispute.js                 # Dispute ticket schema
│   ├── Review.js                  # Product review schema
│   └── DowryEstimation.js         # Dowry budget estimation profile schema
└── services/                      # Microservice HTTP communication clients
    ├── visualClient.js            # Axios client communicating with visual-ml-service (Port 5002)
    ├── mlClient.js                # Axios client communicating with ml-service (Port 5001)
    ├── ruleEngine.js              # Dowry estimation rule-based calculation engine
    └── hybridEngine.js            # Hybrid dowry engine blending rules, DB prices & ML
```

---

## How to Run the Backend

```bash
# 1. Install dependencies
npm install

# 2. Configure environment variables in .env
# Ensure MONGO_URI, JWT_SECRET, PORT=5000, and CLOUDINARY credentials are set

# 3. Start development server with frontend
npm run dev

# Or start backend independently:
node server.js
```
