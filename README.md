# 🚀 CAIRO – AI Agent Platform & Developer SDK

CAIRO is a powerful **AI agent platform** and **developer SDK** designed to help teams build, orchestrate, and deploy intelligent automation workflows safely and at scale. It enables developers to connect LLMs, APIs, tools, and data sources into production-ready agent systems with built-in billing, usage tracking, and multi-tenant organization management.

---

## 🌟 Key Features

### 🤖 AI Agents & Task Execution

- Tool-calling agents with full reasoning pipelines  
- Multi-step task orchestration  
- Memory support and state persistence  
- Streaming and async agent responses  

---

### 📦 Cairo SDK

A TypeScript/Python SDK that provides:

- Task building and execution APIs  
- Agent orchestration utilities  
- Authentication helpers  
- Token management and usage tracking  

**Example:**

~~~ts
import { Cairo } from "cairo-sdk";

const cairo = new Cairo({ apiKey: process.env.CAIRO_API_KEY });

const result = await cairo.runTask({
  prompt: "Draft a product launch announcement",
});
~~~

---

### 🔗 Integrations

CAIRO connects AI agents with real-world tools:

- **Stripe** → payments, wallet top-ups, metered billing  
- **GitHub** → issues, PR creation, code automation  
- APIs & Webhooks for custom integrations  

Planned integrations:

- Slack  
- Notion  
- Google Workspace  
- Jira  

---

## 💳 Wallet, Credits & Billing (Stripe)

### 💼 User Wallet System

- Each organization has a wallet balance.  
- Users can top-up their wallet using Stripe Checkout.  
- Agents consume credits based on model usage and token costs.  
- Balance updates occur in real-time via webhook confirmation.  

---

### 🔄 Billing Flow

#### 1️⃣ Add Balance (Frontend)

User selects amount and clicks **Add Balance**.

Frontend route:

~~~text
POST /payment/create-checkout-session
~~~

---

#### 2️⃣ Stripe Checkout Session Created

Backend creates a Stripe checkout session with metadata:

- `org_id`  
- `user_id`  
- `payment_type = wallet_topup`  

---

#### 3️⃣ Redirect to Stripe

User completes payment securely on Stripe-hosted UI.

---

#### 4️⃣ Stripe Webhook Triggered

Event:

~~~text
checkout.session.completed
~~~

Webhook handler actions:

- Verify webhook signature  
- Retrieve organization + amount  
- Update wallet balance  
- Create ledger credit entry  

---

#### 5️⃣ Wallet Database Update

~~~sql
UPDATE organizations
SET wallet_balance = wallet_balance + <amount>
WHERE id = <org_id>;
~~~

---

#### 6️⃣ Ledger Entry Created

~~~sql
INSERT INTO credit_transactions (
  org_id,
  transaction_type,
  amount,
  source,
  stripe_session_id
)
VALUES (...);
~~~

---

## 🛠️ Backend Architecture

### FastAPI Microservices

CAIRO backend is powered by FastAPI and split into service layers:

~~~text
api/
 ├─ auth/
 ├─ integrations/
 ├─ payments/
 ├─ agents/
 └─ usage/
~~~

---

### Core Backend Components

| Component          | Description                           |
| ------------------ | ------------------------------------- |
| **Auth Service**   | JWT authentication & org verification |
| **Wallet Service** | Balance calculation & validation      |
| **Stripe Service** | Checkout + webhook handler            |
| **Agent Service**  | Task execution engine                 |
| **Usage Service**  | Token usage tracking & metering       |

---

## 🗄️ Database Schema

### Organization Table

~~~sql
organizations (
  id UUID PRIMARY KEY,
  name TEXT,
  wallet_balance DECIMAL,
  created_at TIMESTAMP
);
~~~

---

### Credit Ledger

~~~sql
credit_transactions (
  id UUID PRIMARY KEY,
  org_id UUID FOREIGN KEY,
  transaction_type TEXT,
  amount DECIMAL,
  source TEXT,
  stripe_session_id TEXT,
  created_at TIMESTAMP
);
~~~

---

### API Keys

~~~sql
api_keys (
  id UUID PRIMARY KEY,
  org_id UUID,
  key_hash TEXT,
  is_active BOOLEAN,
  created_at TIMESTAMP
);
~~~

---

## 🔐 Authentication

CAIRO uses JWT-based authentication.

- Each user logs in and receives a JWT token  
- JWT includes:  
  - `user_id`  
  - `org_id`  
  - expiration timestamp  
- API keys are tied to organizations  

Requests validate:

- JWT token  
- Organization membership  
- Wallet balance before task execution  

---

## ⚙️ Agent Execution Flow

1️⃣ User submits task via dashboard or SDK  
2️⃣ Wallet balance checked  
3️⃣ Agent pipeline processes prompt  
4️⃣ Tools + APIs invoked as required  
5️⃣ LLM request executed  
6️⃣ Token usage measured  
7️⃣ Credits deducted from wallet  
8️⃣ Results streamed back to user  

---

## 📊 Usage & Metering

Each task records:

~~~json
{
  "task_id": "uuid",
  "org_id": "uuid",
  "model": "gpt-4",
  "prompt_tokens": 230,
  "completion_tokens": 470,
  "credits_used": 3.93
}
~~~

Credits deducted automatically:

~~~sql
UPDATE organizations
SET wallet_balance = wallet_balance - <credits_used>
WHERE id = <org_id>;
~~~

---

## 🌐 Platform Frontend

**Dashboard Features:**

- Wallet balance view  
- Stripe checkout top-ups  
- Task execution panels  
- Usage reports  
- API key management  

**Built using:**

- React + Next.js  
- Tailwind CSS  
- Secure JWT auth flow  

---

## 🧩 Architecture Diagram

~~~text
User Dashboard
     |
     v
Frontend (Next.js)
     |
     v
FastAPI Gateway
     |
     +--> Auth Service
     |
     +--> Agent Engine
     |
     +--> Stripe Service ----> Stripe
     |
     +--> Usage & Billing
     |
     v
PostgreSQL Database
~~~

---

## 🛡️ Security

- Encrypted secrets via environment variables  
- Stripe webhook signature verification  
- JWT token validation per request  
- API key hashing  
- Rate limiting on task creation  

---

## 🗺️ Roadmap

✅ Developer SDK stable release  
✅ Stripe billing + wallet system  
✅ GitHub integration foundation  

🚧 In Progress:

- Team collaboration agents  
- Usage alerting dashboard  
- Multi-agent workflows UI  

🔜 Planned:

- Slack & Notion integrations  
- Marketplace for custom agents  
- AI workflow templates  

---

## 🤝 Contributing

We welcome contributions!

1. Fork the repository  
2. Create a feature branch:

   ~~~bash
   git checkout -b feature/my-feature
   ~~~

3. Commit your changes  
4. Open a pull request  

---

## 📬 Contact

For platform access or partnership inquiries:

📧 [support@cairo.ai](mailto:support@cairo.ai)  
🌍 Website: <https://cairo.ai> (launching soon)  

---

## 🚀 Getting Started

Install SDK:

~~~bash
npm install cairo-sdk
~~~

Initialize:

~~~ts
const cairo = new Cairo({ apiKey: "YOUR_API_KEY" });
~~~

Run your first task:

~~~ts
await cairo.runTask({
  prompt: "Summarize today's market news",
});
~~~

---

**Build smarter with CAIRO — where AI agents go to production.**
