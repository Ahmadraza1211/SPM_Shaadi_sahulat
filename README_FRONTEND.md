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

# Frontend Application Documentation (React & Vite)

The frontend is a single-page application (SPA) built using React 18, Vite, and Tailwind CSS. It provides tailored portals for buyers, sellers, bank verification officers, and platform administrators.

---

## Architecture & Global State

The client uses React Context Providers to maintain persistent state and handle cross-cutting concerns:

1. **`AuthContext.jsx`**:
   - Manages user login state, role permissions (`buyer`, `seller`, `banker`, `admin`), profile data, and token refresh.
   - Injects the authenticated `Authorization: Bearer <token>` header into all outbound Axios/fetch requests.
2. **`CartContext.jsx`**:
   - Stores transient shopping cart items in local storage.
   - Calculates item totals, handles size/quantity adjustments, and groups items by seller for checkout.
3. **`NotificationContext.jsx`**:
   - Establishes a real-time Socket.IO connection on login (`http://localhost:5000`).
   - Listens for live alerts (order updates, shipping progress, dispute replies, and BNPL approvals).

---

## Component Structure & UI Modules

All UI views are located under `src/components/`:

```text
src/
├── App.jsx                        # Main React application router & view container
├── index.css                      # Global styles & Tailwind CSS utility classes
├── context/                       # Global state context providers
│   ├── AuthContext.jsx            # User authentication & JWT state
│   ├── CartContext.jsx            # Shopping cart & checkout state
│   └── NotificationContext.jsx    # Real-time WebSocket event subscriber
│
└── components/                    # Feature-specific page components
    ├── Marketplace/               # E-Commerce shopping
    │   ├── MarketplacePage.jsx    # Storefront browsing, search & category filtering
    │   ├── ProductDetailPage.jsx  # Detailed dress view, reviews, specs & try-on trigger
    │   └── SizeAwareTryOnModal.jsx# Interactive modal for fit verdict & visual try-on
    ├── Cart/                      # Order checkout
    │   ├── CartPage.jsx           # Cart item inspection & quantity controls
    │   └── CheckoutPage.jsx       # Multi-seller checkout with OpenStreetMap address search
    ├── BNPL/                      # Buy Now Pay Later financing
    │   ├── BNPLApplyPage.jsx      # Financing application form, bank picker & file upload
    │   └── BNPLStatusPage.jsx     # Active installment tracking & repayment logs
    ├── Orders/                    # Buyer order tracking
    │   ├── BuyerOrdersPage.jsx    # Order history & status badges
    │   └── BuyerOrderDetailPage.jsx# Order tracking progress bar, package delivery confirm & dispute trigger
    ├── Bank/                      # Banker approval portal
    │   └── BankDashboardPage.jsx  # Bank officer portal for reviewing applications & recording installments
    ├── Seller/                    # Seller merchant portal
    │   ├── SellerDashboard.jsx    # Store metrics, revenue stats & inventory overview
    │   ├── ProductUpload.jsx      # Product listing creation & image upload
    │   ├── SellerOrdersPage.jsx   # Package fulfillment, status transitions & tracking codes
    │   └── SellerFinancialProjection.jsx # Escrow payout balances & withdrawal requests
    ├── Buyer/                     # Buyer account & budget tracking
    │   ├── BuyerDashboard.jsx     # Spending breakdown vs dowry budget target
    │   └── FinalProjection.jsx    # Projected savings & expense forecast
    ├── Dowry/                     # Dowry budget estimation wizard
    │   ├── DowryPage.jsx          # Wizard container & active step controller
    │   ├── StepFamily.jsx         # Family members & dependent unmarried children inputs
    │   ├── StepFinancial.jsx      # Household income, savings & contribution inputs
    │   ├── StepPriority.jsx       # Category priority sliders (High, Medium, Low, Not Wanted)
    │   └── StepResults.jsx        # Category allocation breakdown chart & ML adjustment badges
    ├── VisualRec/                 # AI visual dress matching
    │   ├── VisualRecPage.jsx      # Visual match page container
    │   ├── ImageUpload.jsx        # Image drag-and-drop file uploader
    │   └── ResultsGrid.jsx        # Visual match cards with similarity percentage badges
    ├── Reviews/                   # Customer review system
    │   ├── ReviewForm.jsx         # Order-gated star rating & feedback form
    │   └── ReviewList.jsx         # Product reviews list with AI sentiment summary badge
    └── Common/                    # Reusable layout elements
        ├── BuyerPageHero.jsx      # Luxury banner header for buyer pages
        ├── SellerPageHero.jsx     # Header component for seller dashboard pages
        └── OrderTimeline.jsx      # Visual timeline tracker for package delivery
```

---

## Key Frontend Features

1. **Address Autocomplete & Geocoding**:
   - `CheckoutPage.jsx` integrates the OpenStreetMap Nominatim API (`countrycodes=pk`) to provide live address auto-suggestions and lat/long coordinates for shipping.
2. **Visual Dress Recommendation Grid**:
   - `VisualRecPage.jsx` allows buyers to drag and drop a dress photo and view matching products ranked by vision-first similarity scores.
3. **Interactive Size-Aware Fit Verdict**:
   - `SizeAwareTryOnModal.jsx` lets buyers enter height and body dimensions to receive an instant fit estimate (`FIT`, `TOO_SMALL`, `TOO_LARGE`).
4. **Multi-Role Portal Views**:
   - Tailored interfaces for Buyers, Sellers, Bank Officers, and Administrators based on the authenticated user's role.

---

## How to Run the Frontend

```bash
# 1. Install dependencies
npm install

# 2. Run Vite development server
npm run dev
# The application will be available at http://localhost:3000
```
