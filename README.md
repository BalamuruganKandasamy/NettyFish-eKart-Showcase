# 🛒 NettyFish eKart — Multi-Tenant SaaS E-Commerce Platform

> **Built 100% via AI Prompt Engineering** — system design → data model → API → frontend → deployment, all delivered through structured multi-turn Claude prompt chains. Zero manual boilerplate.

🔗 **Live Demo:** [nettyfish-ekart-dev-2026.web.app](https://nettyfish-ekart-dev-2026.web.app)

---

## 🎯 What It Does

NettyFish eKart is a **production-grade multi-tenant SaaS e-commerce platform** for retail businesses — grocery chains, vegetable stores, and local outlets. A single platform instance serves multiple companies, each with their own branded storefront, outlet locations, and customer base.

**Real-world use case:** "ABC Grocery" runs 5 Chennai outlets. "XYZ Vegetables" runs 3 outlets. Both share the same platform infrastructure — fully isolated data, independent configuration, independent payment and SMS providers.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Customer Browser                      │
│         React (Vite) + Tailwind + Zustand               │
└────────────────────┬────────────────────────────────────┘
                     │ REST API + Socket.IO
                     ▼
┌─────────────────────────────────────────────────────────┐
│              Node.js + Express API                       │
│                                                         │
│  ┌──────────────────┐   ┌──────────────────────────┐   │
│  │ Tenant Resolver  │   │   JWT Auth + Role Gate   │   │
│  │ (X-Subdomain /   │   │  super_admin │ company_  │   │
│  │  real subdomain) │   │  admin │ outlet_manager  │   │
│  └──────────────────┘   └──────────────────────────┘   │
│                                                         │
│  ┌───────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐  │
│  │  Payment  │ │   SMS    │ │  Order   │ │  Geo    │  │
│  │ Adapter   │ │ Adapter  │ │ Engine   │ │ Search  │  │
│  │(Strategy) │ │(Strategy)│ │+Socket.IO│ │$geoNear │  │
│  └───────────┘ └──────────┘ └──────────┘ └─────────┘  │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                  MongoDB                                 │
│  Company · Store · Product · Order · Customer · OTP     │
│  Every tenant collection scoped by companyId            │
└─────────────────────────────────────────────────────────┘
```

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React (Vite) · Tailwind CSS v4 · React Router · Zustand · Axios · Socket.IO client |
| **Backend** | Node.js · Express · Mongoose · Socket.IO · JWT · bcryptjs |
| **Database** | MongoDB (geo-indexed, multi-tenant scoped) |
| **Cloud** | GCP Cloud Run · Firebase Hosting |
| **Auth** | OTP login (phone/email) · JWT · Role-based access control |
| **Real-time** | Socket.IO — live order alerts to outlet dashboard |

---

## 🚀 Key Features

### 🏢 Multi-Tenancy (Core Architecture)
- Every data collection (`Store`, `Product`, `Order`, `Customer`) is scoped by `companyId`
- Tenant resolved from subdomain (`abc.nettyfish.com`) or header in dev
- Zero cross-tenant data leakage by design
- Payment and SMS providers **configurable per company** via Strategy pattern adapters

### 🔐 Authentication
- OTP-based customer login (phone or email)
- JWT auth with role gates: `super_admin` / `company_admin` / `outlet_manager`
- `Customer` and `PlatformUser` are separate models (shoppers vs. staff)

### 📍 Geolocation
- Nearby store search using MongoDB `$geoNear` (geo-indexed stores)
- Delivery radius validation (Haversine check) — home delivery only offered if customer is within store's radius

### 🛍️ Shopping Flow
- Product catalog with variants (each variant has its own price + stock)
- Cart persisted in browser (Zustand + localStorage) — survives page refresh
- Full checkout: Home Delivery or Store Pickup
- Server-side stock validation and decrement at order time (never trust client)
- Prices snapshotted onto Order at creation — historical order display is always accurate

### ⚡ Real-Time Order Alerts
- New orders emit Socket.IO event to room `outlet:<storeId>`
- Outlet dashboard receives live "new order" alert with accept/reject actions

### 🔌 Extensible Adapters
- Payment: plug in Razorpay, Stripe, or any provider by adding one adapter file
- SMS: plug in MSG91, Twilio, or any provider — no logic scattered in routes

---

## 📁 Project Structure

```
Repo/
├── NettyFish_eKart/              # Frontend
│   └── src/
│       ├── api/
│       │   ├── client.js         # Axios instance — tenant header + JWT auto-attached
│       │   └── endpoints.js      # All API calls grouped by domain
│       ├── store/                # Zustand: authStore · cartStore · storeSelection
│       ├── components/           # Header · BottomNav · Layout · ProductCard
│       └── pages/                # HomePage · StoreSelection · ProductListing
│                                 # ProductDetail · Cart · Checkout · OrderHistory
│
└── NettyFish_eKart_API/          # Backend
    └── src/
        ├── middleware/
        │   ├── tenantResolver.js  # Resolves req.company / req.companyId
        │   └── auth.js            # JWT verify + role gate
        ├── models/                # Company · Store · Product · Order · Customer · Otp · PlatformUser
        ├── services/
        │   ├── payment/paymentFactory.js   # Strategy adapter
        │   └── sms/smsFactory.js           # Strategy adapter
        └── server.js              # Entry point + Socket.IO setup
```

---

## 📊 By the Numbers

| Metric | Value |
|---|---|
| Tenants supported | Unlimited (shared infrastructure) |
| Outlet locations per tenant | Unlimited |
| Auth method | OTP (phone or email) + JWT |
| Real-time tech | Socket.IO |
| Delivery validation | Haversine geo-radius check |
| Adapter pattern used | Payment + SMS (plug any provider) |
| Roles | super_admin · company_admin · outlet_manager |
| Build method | 100% AI Prompt Engineering (Claude) |

---

## 🤖 How This Was Built

This entire platform — system design, Mongoose data models, Express API, React components, Firebase deployment config — was delivered through **structured multi-turn prompt engineering** with Claude.

No manual boilerplate. No scaffolding generators. Each phase was:
1. **Context given** — domain rules, business logic, architectural constraints
2. **Claude architected** — entity models, API contracts, component structure
3. **Claude coded** — full implementation with conventions enforced
4. **Iterated** — multi-turn refinement until production-ready

This is **LLM-native development** — not autocomplete, but Claude as a product co-developer.

---

## 🗺️ Roadmap

- [ ] Outlet & company admin dashboards (accept/reject orders + live alert sound)
- [ ] Google Sheets / CSV inventory sync
- [ ] Razorpay payment gateway integration
- [ ] Real SMS gateway (MSG91 / Twilio)
- [ ] Refund flow
- [ ] CI/CD pipeline

---

## 👨‍💻 Built By

**Balamurugan Kandasamy (Bala)** — AI Prompt Engineer · Full Stack Dev · 18+ Years Enterprise

📧 baluclick@gmail.com | 📍 Chennai, India | ✈️ Open to Middle East / Remote

> *"I don't use AI to autocomplete lines of code. I use it as a product co-founder — providing domain context, business rules, and iterative feedback while Claude architects, codes, and deploys entire feature sets."*
