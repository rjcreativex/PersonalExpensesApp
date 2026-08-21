# Personal Expenses App — Product & Solution Architecture

## 1. Product Vision

Build a personal finance management platform that helps users:

- Track income and expenses
- Understand spending behavior
- Create and manage budgets
- Monitor recurring bills and subscriptions
- Build savings goals
- Forecast future cash flow
- Improve overall financial health

The product should evolve beyond a basic expense tracker into a **Personal Financial Command Center**.

### Core Product Journey

```text
Track
  ↓
Understand
  ↓
Budget
  ↓
Plan
  ↓
Predict
  ↓
Improve
```

---

## 2. Target Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js + TypeScript |
| UI | Tailwind CSS + shadcn/ui |
| Charts | Recharts |
| Authentication | Auth.js |
| Backend/API | Next.js Route Handlers / Server Actions |
| Validation | Zod |
| Database | PostgreSQL |
| ORM | Drizzle ORM |
| Cache | Redis — later |
| Background Jobs | BullMQ — later |
| Error Monitoring | Sentry |
| Testing | Vitest + Playwright |
| CI/CD | GitHub Actions |
| Hosting | Vercel |
| Database Hosting | Neon / Supabase / AWS RDS |

---

## 3. Market Research

### YNAB

YNAB is strong in proactive budgeting, savings goals, account synchronization, spending reports, net-worth visibility, and household sharing.

Key ideas worth adopting:

- Goal-based budgeting
- Category targets
- Monthly summaries
- Overspending alerts
- Planned vs actual spending

Potential improvement:

Use a simpler initial mental model:

```text
Income → Budget → Spending → Remaining
```

---

### Monarch Money

Monarch provides a broad personal financial dashboard with customizable categories, transaction tags, financial goals, and account aggregation.

Key ideas worth adopting:

- Categories + tags
- Household financial management
- Goal tracking
- High-level financial overview

Example:

```text
Transaction:
Starbucks ₱250

Category:
Food > Coffee

Tags:
Work
Meeting
Reimbursable
```

---

### Rocket Money

Rocket Money focuses heavily on:

- Subscription detection
- Spending analytics
- Budgeting
- Balance alerts
- Net-worth tracking
- Savings goals

Useful product concept:

```text
Netflix    ₱549     Monthly
Spotify    ₱149     Monthly
ChatGPT    ₱1,200   Monthly
Internet   ₱1,699   Monthly

Recurring monthly expenses
₱3,597

Projected annual cost
₱43,164
```

---

### Spendee

Spendee supports:

- Cash wallets
- Bank accounts
- Budgeting
- Automatic categorization
- Shared wallets
- Scheduled transactions
- Imports and exports
- Analytics

Important use cases:

```text
Couples
Families
Roommates
Trips
Events
```

---

### Wallet by BudgetBakers

Wallet emphasizes financial visibility and helping users understand their behavior instead of merely logging transactions.

Example insight:

```text
Dining spending increased 22% this month.

You spent ₱4,250 more than your
3-month dining average.

Reducing dining by ₱1,500/month
would save ₱18,000/year.
```

---

## 4. Competitive Feature Direction

| Capability | Proposed App |
|---|---|
| Expense tracking | MVP |
| Income tracking | MVP |
| Categories | MVP |
| Custom tags | MVP |
| Monthly budgets | MVP |
| Savings goals | MVP |
| Recurring expenses | MVP |
| Reports | MVP |
| Subscription detection | Phase 2 |
| Shared wallets | Phase 2 |
| Net worth | Phase 2 |
| Forecasting | Phase 2 |
| Bank synchronization | Phase 3 |
| Automatic categorization | Phase 3 |
| AI financial insights | Phase 4 |
| Natural-language analytics | Phase 4 |

---

# 5. MVP Scope

The first version should contain these modules:

1. Authentication
2. Dashboard
3. Accounts / Wallets
4. Transactions
5. Categories
6. Tags
7. Budgets
8. Recurring Expenses
9. Savings Goals
10. Reports
11. User Settings

The MVP objective:

> A user can completely track and understand personal finances manually without requiring bank integrations.

---

# 6. Main User Flow

```text
LOGIN

   ↓

DASHBOARD

   ├── Total Balance
   ├── Income
   ├── Expenses
   ├── Remaining Budget
   ├── Upcoming Bills
   └── Savings Progress

            ↓

ACCOUNTS

   ├── Cash
   ├── Bank
   ├── E-Wallet
   ├── Credit Card
   └── Savings

            ↓

TRANSACTIONS

   ├── Expense
   ├── Income
   └── Transfer
```

---

# 7. Dashboard

Example:

```text
August 2026
──────────────────────────────────

TOTAL BALANCE
₱156,450

INCOME
₱95,000

EXPENSES
₱52,340

AVAILABLE
₱42,660
```

### Spending by Category

```text
Food             ₱12,300  24%
Housing          ₱15,000  29%
Transportation    ₱6,400  12%
Shopping          ₱5,500  11%
Utilities         ₱4,300   8%
Others            ₱8,840  16%
```

### Monthly Budget

```text
₱52,340 / ₱70,000

████████████████░░░░

75% used

₱17,660 remaining
```

---

# 8. Account Domain

Do not attach every transaction directly to the user only.

Each transaction should belong to an account or wallet.

### Supported Account Types

```text
CASH
BANK
EWALLET
CREDIT_CARD
SAVINGS
INVESTMENT
LOAN
OTHER
```

Example:

```text
BDO Savings
₱84,000

BPI
₱32,500

GCash
₱11,350

Cash
₱5,600

Credit Card
-₱8,400
```

---

# 9. Transaction Domain

Transactions are the core domain object.

```typescript
type TransactionType =
  | "EXPENSE"
  | "INCOME"
  | "TRANSFER";

interface Transaction {
  id: string;
  userId: string;
  accountId: string;
  type: TransactionType;
  amount: string;
  currency: string;
  categoryId?: string;
  merchant?: string;
  description?: string;
  transactionDate: Date;
  source: TransactionSource;
  createdAt: Date;
  updatedAt: Date;
}
```

### Transaction Sources

```text
MANUAL
IMPORT
BANK_SYNC
RECURRING
SYSTEM
```

### Transfer Behavior

A transfer should not be counted as an expense.

Example:

```text
BDO
- ₱5,000

GCash
+ ₱5,000
```

The entries should be linked by a transfer reference.

---

# 10. Categories

Use hierarchical categories.

```text
Food
 ├── Groceries
 ├── Restaurant
 ├── Coffee
 └── Delivery

Transportation
 ├── Fuel
 ├── Taxi
 ├── Grab
 └── Public Transport

Housing
 ├── Rent
 ├── Electricity
 ├── Water
 └── Internet
```

Suggested table:

```text
categories
------------------
id
user_id
parent_id
name
icon
type
is_system
created_at
updated_at
```

---

# 11. Tags

Tags should be independent of categories.

Examples:

```text
#vacation
#work
#family
#business
#reimbursement
#japan-trip
```

Possible query:

```text
Show all expenses tagged #japan-trip
```

---

# 12. Budget Engine

Example:

```text
Monthly Budget
August 2026

Food
₱12,000

Transport
₱8,000

Entertainment
₱4,000

Shopping
₱5,000
```

Progress:

```text
Budget
₱12,000

Spent
₱9,400

Remaining
₱2,600

Usage
78%
```

Alert thresholds:

```text
50%
75%
90%
100%
```

---

# 13. Recurring Expenses

Suggested frequencies:

```text
DAILY
WEEKLY
BIWEEKLY
MONTHLY
QUARTERLY
YEARLY
CUSTOM
```

Example:

```text
Netflix

Amount
₱549

Frequency
MONTHLY

Next Payment
August 25

Category
Entertainment
```

This module can later support automatic subscription detection.

---

# 14. Savings Goals

Examples:

```text
Emergency Fund
Vacation
Car
House
Laptop
Wedding
Education
Investment
```

Example:

```text
Emergency Fund

Goal
₱150,000

Saved
₱83,500

Progress
56%
```

Suggested schema:

```text
financial_goals
----------------
id
user_id
name
target_amount
current_amount
target_date
status
created_at
updated_at
```

---

# 15. Reports

Initial reports:

- Spending by Category
- Income vs Expenses
- Monthly Spending Trend
- Budget vs Actual
- Cash Flow
- Top Merchants
- Recurring Expenses
- Savings Rate

Later reports:

- Net Worth
- Debt Ratio
- Emergency Fund Coverage
- Average Daily Spend
- Projected Month-End Balance

---

# 16. Financial Health Score

This can become a differentiating feature.

Example:

```text
FINANCIAL HEALTH SCORE

78 / 100
GOOD
```

Possible factors:

- Savings rate
- Budget discipline
- Debt ratio
- Emergency fund coverage
- Recurring cost ratio
- Income-to-expense ratio

Example:

```text
Savings Rate
21%
Good

Budget Usage
73%
Healthy

Recurring Expenses
18%
Moderate

Emergency Fund
2.7 months
Needs improvement
```

---

# 17. Recommended Architecture

Use a **Modular Monolith** initially.

Do not start with microservices.

```text
                    USERS
                       │
                       ▼
                ┌─────────────┐
                │   Next.js   │
                │    Web      │
                └──────┬──────┘
                       │
                       ▼
             ┌──────────────────┐
             │ Next.js Backend  │
             │ Route Handlers   │
             │ Server Actions   │
             └─────────┬────────┘
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
        Application       Background Jobs
          Services             Later
              │
              ▼
        Repository Layer
              │
              ▼
          PostgreSQL
```

---

# 18. Next.js Project Structure

```text
src/
│
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   │
│   ├── (dashboard)/
│   │   ├── dashboard/
│   │   ├── accounts/
│   │   ├── transactions/
│   │   ├── budgets/
│   │   ├── goals/
│   │   ├── reports/
│   │   └── settings/
│   │
│   └── api/
│
├── modules/
│   ├── accounts/
│   ├── transactions/
│   ├── categories/
│   ├── budgets/
│   ├── goals/
│   ├── reports/
│   └── users/
│
├── components/
├── lib/
├── db/
├── services/
└── types/
```

Example module:

```text
transactions/

transaction.controller.ts
transaction.service.ts
transaction.repository.ts
transaction.schema.ts
transaction.types.ts
```

Architectural layering:

```text
Presentation

      ↓

Application

      ↓

Domain

      ↓

Infrastructure
```

---

# 19. PostgreSQL Schema Overview

Core tables:

```text
users

accounts

transactions

transaction_transfers

categories

tags

transaction_tags

budgets

budget_categories

recurring_transactions

financial_goals

goal_contributions

notifications

user_preferences

audit_logs
```

Later:

```text
bank_connections

bank_transactions

merchants

subscriptions

households

household_members

net_worth_snapshots
```

---

# 20. Relational Model

```text
USER
 │
 ├──────── ACCOUNTS
 │             │
 │             └──── TRANSACTIONS
 │                        │
 │                        ├──── CATEGORY
 │                        │
 │                        └──── TAGS
 │
 ├──────── BUDGETS
 │             │
 │             └──── BUDGET_CATEGORIES
 │
 ├──────── GOALS
 │
 └──────── RECURRING_TRANSACTIONS
```

---

# 21. Financial Data Types

Do not depend on JavaScript floating-point arithmetic for money.

PostgreSQL recommendation:

```sql
amount NUMERIC(19,4) NOT NULL
```

Avoid financial calculations based directly on:

```typescript
number
```

Use decimal-safe logic.

---

# 22. Multi-Currency Design

Design multi-currency support from the beginning.

Recommended fields:

```text
amount
currency
exchange_rate
base_amount
base_currency
```

Example:

```text
amount         100
currency       USD

exchange_rate  58.25

base_amount    5825
base_currency  PHP
```

---

# 23. API Design

### Transactions

```http
POST /api/v1/transactions

GET /api/v1/transactions

GET /api/v1/transactions/:id

PATCH /api/v1/transactions/:id

DELETE /api/v1/transactions/:id
```

Filtering:

```http
GET /api/v1/transactions
    ?from=2026-08-01
    &to=2026-08-31
    &category=food
    &account=bdo
    &type=expense
    &page=1
    &limit=50
```

### Budgets

```http
POST /api/v1/budgets

GET /api/v1/budgets/current

GET /api/v1/budgets/:id/progress
```

### Dashboard

```http
GET /api/v1/dashboard/summary
```

Example response:

```json
{
  "balance": 156450,
  "income": 95000,
  "expenses": 52340,
  "savings": 42660,
  "budgetUsedPercentage": 74.77
}
```

---

# 24. Authentication

Recommended:

```text
Auth.js
```

Potential providers:

- Email/password
- Google
- Apple

Architecture:

```text
User
  ↓
Auth.js
  ↓
Session
  ↓
Next.js middleware
  ↓
Protected application
```

Never trust a `userId` received directly from the client for authorization.

The authenticated user must come from the server-side session.

---

# 25. Security

Minimum requirements:

- HTTPS
- Secure session cookies
- CSRF protection where applicable
- Input validation
- Rate limiting
- Authorization
- Encryption at rest
- Encrypted backups
- Audit logs
- Database least privilege
- Security headers
- Secret management

Critical requirement:

```text
resource.user_id === authenticatedUser.id
```

All resource access must be tenant/user scoped.

---

# 26. Validation

Recommended validation library:

```text
Zod
```

Example:

```typescript
const CreateTransactionSchema = z.object({
  accountId: z.string().uuid(),

  type: z.enum([
    "EXPENSE",
    "INCOME",
    "TRANSFER"
  ]),

  amount: z.string(),

  categoryId: z.string().uuid().optional(),

  description: z.string().max(255).optional(),

  transactionDate: z.coerce.date()
});
```

---

# 27. ORM Recommendation

Recommended default:

```text
Next.js
PostgreSQL
Drizzle ORM
Zod
```

Reason:

- Better SQL visibility
- Strong PostgreSQL control
- Good fit for a financial application
- Easier to reason about exact queries and indexes

Prisma is still a strong alternative if developer speed is prioritized.

---

# 28. Caching

Do not introduce Redis immediately.

Initial:

```text
Next.js
    ↓
PostgreSQL
```

Later:

```text
Next.js
    │
    ├── Redis
    │
    └── PostgreSQL
```

Possible Redis uses:

- Rate limiting
- Dashboard caching
- Sessions
- Distributed locks
- Background-job coordination

---

# 29. Background Jobs

Future async workloads:

- Recurring transaction generation
- Budget notifications
- Monthly reports
- Subscription detection
- Bank synchronization
- Email notifications
- Financial insights

Architecture:

```text
Next.js
   │
   ▼
Job Queue
   │
   ▼
Worker
   │
   ▼
PostgreSQL
```

Possible implementation:

```text
BullMQ + Redis
```

---

# 30. Bank Integration

Bank synchronization should be Phase 3.

Architecture:

```text
Bank
  │
  ▼
Financial Aggregator
  │
  ▼
Bank Integration Module
  │
  ▼
Raw Transactions
  │
  ▼
Normalization
  │
  ▼
Categorization
  │
  ▼
Transactions
```

The transaction schema must support imported data from day one.

---

# 31. AI Features

Do not make AI part of the first MVP.

Potential future AI features:

## Automatic Categorization

```text
Starbucks
→ Food / Coffee

Shell
→ Transportation / Fuel

Meralco
→ Utilities / Electricity
```

## Spending Insights

```text
Your food spending increased 31%
compared with last month.
```

## Anomaly Detection

```text
This ₱18,500 transaction is significantly
higher than your normal shopping expenses.
```

## Natural-Language Analytics

Example user query:

```text
How much did I spend eating outside during the last three months?
```

The system converts the request into structured analytics filters.

---

# 32. Forecasting Engine

Potential formula:

```text
Current balance

+

Expected salary

-

Upcoming bills

-

Estimated variable expenses

=

Projected month-end balance
```

Example:

```text
Current Balance
₱44,200

Expected Income
+ ₱45,000

Upcoming Bills
- ₱19,500

Estimated Spending
- ₱17,000

────────────────

Projected Aug 31 Balance

₱52,700
```

---

# 33. Analytics Architecture

Perform aggregations in PostgreSQL rather than calculating everything in frontend JavaScript.

Example:

```sql
SELECT
    category_id,
    SUM(amount)
FROM transactions
WHERE
    user_id = $1
    AND type = 'EXPENSE'
    AND transaction_date >= $2
    AND transaction_date < $3
GROUP BY category_id;
```

Optimization options later:

- Indexes
- Materialized views
- Summary tables
- Redis caching

---

# 34. Recommended Indexes

```sql
CREATE INDEX idx_transactions_user_date
ON transactions(user_id, transaction_date DESC);

CREATE INDEX idx_transactions_account
ON transactions(account_id);

CREATE INDEX idx_transactions_category
ON transactions(category_id);

CREATE INDEX idx_transactions_user_type
ON transactions(user_id, type);
```

A critical query pattern will be:

```text
Get this user's transactions within a date range.
```

---

# 35. Audit Logs

Suggested schema:

```text
audit_logs
----------------
id
user_id
action
entity_type
entity_id
old_value
new_value
ip_address
user_agent
created_at
```

Example:

```text
TRANSACTION_UPDATED

Previous
₱500 Food

New
₱5,000 Food
```

Audit logs should generally be append-only.

---

# 36. Observability

Production requirements:

- Application logs
- Error tracking
- Performance monitoring
- Database metrics
- Audit logs
- Request correlation IDs

Recommended log context:

```text
requestId
userId
endpoint
duration
status
```

Never log:

- Passwords
- Session tokens
- Bank credentials
- Sensitive financial secrets

---

# 37. Deployment Architecture

Initial:

```text
                 Internet
                    │
                    ▼
                 Vercel
              Next.js App
                    │
                    ▼
              PostgreSQL
          Neon / Supabase /
             AWS RDS
```

Later:

```text
               CDN
                │
                ▼
             Next.js
                │
         ┌──────┴───────┐
         ▼              ▼
       Redis        PostgreSQL
                        │
                        ▼
                    Backups
```

---

# 38. Development Roadmap

## Phase 1 — Core MVP

- Authentication
- Accounts
- Transactions
- Categories
- Tags
- Dashboard
- Monthly budgets
- Reports
- Recurring expenses
- Savings goals

Goal:

> A user can completely track and understand personal finances manually.

---

## Phase 2 — Smart Personal Finance

Add:

- Shared wallets
- Subscriptions
- Advanced reporting
- CSV imports
- Notifications
- Financial-health score
- Net worth
- Forecasting

---

## Phase 3 — Automation

Add:

- Bank synchronization
- Automatic transaction import
- Automatic categorization
- Recurring-payment detection
- Duplicate detection
- Merchant normalization

---

## Phase 4 — Intelligent Finance

Add:

- AI insights
- Natural-language queries
- Spending predictions
- Expense anomaly detection
- Budget recommendations
- Savings recommendations

---

# 39. Technologies to Avoid Initially

Do not start the project with:

- Microservices
- Kafka
- Kubernetes
- Event sourcing
- CQRS
- AI
- Bank integrations
- Multiple databases
- Blockchain

Initial architecture:

```text
Next.js
   +
PostgreSQL
   +
Modular Monolith
```

Scale only when there is a demonstrated business or technical requirement.

---

# 40. Final Product Direction

Do not position the product simply as:

> Expense Tracker

Position it as:

> **Personal Financial Command Center**

The product should help users:

- Track money
- Understand spending
- Control budgets
- Plan future expenses
- Predict cash flow
- Improve financial behavior

For V1, prioritize:

1. Excellent manual transaction management
2. Accounts and wallets
3. Categories and tags
4. Monthly budgeting
5. Recurring bills
6. Dashboard analytics
7. Savings goals
8. Reports

Design the database from day one for future:

- Bank transaction imports
- Multi-currency
- Shared households
- Subscription detection
- AI categorization
- Forecasting
- Financial recommendations
