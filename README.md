# PH-Salon-Manager

**PH-Salon-Manager** is a high-performance, mobile-first salon management solution built with **React Native (Expo)** and a **Node.js/TypeScript** backend. Designed for commercial reliability, it streamlines appointment booking, staff management, and inventory tracking with enterprise-grade security.

## 🚀 Key Features

* **Real-time Appointments:** WebSocket-powered scheduling for instant updates.
* **Secure Authentication:** JWT-based login with bcrypt password hashing and token refresh logic.
* **Role-Based Access:** Distinct workflows for Admins, Staff, and Customers.
* **Inventory & Payments:** Integrated tracking of salon supplies and payment processing.
* **Premium UI:** Glassmorphism effects using `expo-blur` and smooth animations via `react-native-reanimated`.

## 🛠️ Tech Stack

### Frontend

* **Framework:** React Native 0.81.5 (Expo 54)
* **Navigation:** Expo Router
* **State Management:** React Query (TanStack)
* **Validation:** Zod Schema Library

### Backend

* **Runtime:** Node.js (TypeScript)
* **Server:** Express.js 5.0.1
* **Database:** PostgreSQL with **Drizzle ORM**
* **Security:** Rate limiting, CORS protection, and encrypted audit logs.

## 📁 Project Structure

```text
├── app/                # Expo Router mobile screens
├── server/             # Node.js backend
│   ├── db/            # Database schema and migrations
│   ├── middleware/    # Auth, Logger, Rate-limiter
│   └── routes/        # API Endpoints
├── shared/             # Shared TypeScript types and Zod schemas
└── assets/             # Images and design resources

```

## ⚙️ Setup & Installation

### Prerequisites

* Node.js (v18+)
* PostgreSQL Database
* Expo Go app (for mobile testing)

### 1. Clone & Install

```bash
git clone https://github.com/phexclusive123-code/PH-Salon-Manager.git
cd PH-Salon-Manager
npm install

```

### 2. Environment Configuration

Create a `.env` file in the root directory:

```env
DATABASE_URL=postgresql://user:pass@localhost/salon
JWT_SECRET=your_secure_secret
NODE_ENV=development

```

### 3. Run the Project

* **Start Database:** `npm run db:push`
* **Start Backend:** `npm run server:dev`
* **Start Frontend:** `npx expo start`

## 🔐 Security & Reliability

This application implements several measures to ensure "no-break" performance:

* **Safe JSON Parsing:** All API responses are wrapped in recovery blocks to prevent crashes.
* **Input Sanitization:** All user inputs are validated via Zod before hitting the database.
* **Audit Trail:** Detailed logging for all critical administrative actions.

---

### 📄 License

This project is proprietary and intended for commercial use by **PH Salon**.



