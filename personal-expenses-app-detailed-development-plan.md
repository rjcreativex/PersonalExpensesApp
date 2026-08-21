# Personal Expenses App --- Detailed Development Plan

## 1. Purpose

This development plan is derived only from the approved **Personal
Expenses App --- Product & Solution Architecture** document.

It does not introduce additional product features, technologies,
integrations, implementation estimates, sprint durations, team sizes, or
requirements that are not already defined in the source architecture.

The target product is a **Personal Financial Command Center** that
allows users to track money, understand spending, control budgets, plan
future expenses, predict cash flow, and improve financial behavior.

------------------------------------------------------------------------

# 2. Development Principles

## 2.1 Architecture Strategy

The application will begin as a **Modular Monolith**.

Initial architecture:

``` text
Next.js
   +
PostgreSQL
   +
Modular Monolith
```

The application must not initially introduce:

-   Microservices
-   Kafka
-   Kubernetes
-   Event sourcing
-   CQRS
-   AI
-   Bank integrations
-   Multiple databases
-   Blockchain

Scaling or architectural expansion should happen only when there is a
demonstrated business or technical requirement.

## 2.2 MVP Objective

The first development objective is:

> A user can completely track and understand personal finances manually
> without requiring bank integrations.

## 2.3 Product Priority

V1 development must prioritize:

1.  Excellent manual transaction management
2.  Accounts and wallets
3.  Categories and tags
4.  Monthly budgeting
5.  Recurring bills
6.  Dashboard analytics
7.  Savings goals
8.  Reports

------------------------------------------------------------------------

# 3. Approved Technology Stack

Development must use the technologies already defined in the
architecture.

  Layer              Technology
  ------------------ -----------------------------------------
  Frontend           Next.js + TypeScript
  UI                 Tailwind CSS + shadcn/ui
  Charts             Recharts
  Authentication     Auth.js
  Backend/API        Next.js Route Handlers / Server Actions
  Validation         Zod
  Database           PostgreSQL
  ORM                Drizzle ORM
  Cache              Redis --- later
  Background Jobs    BullMQ --- later
  Error Monitoring   Sentry
  Testing            Vitest + Playwright
  CI/CD              GitHub Actions
  Hosting            Vercel
  Database Hosting   Neon / Supabase / AWS RDS

Redis and BullMQ are later-stage technologies and must not be introduced
into the initial implementation unless the later capabilities described
in the architecture are being implemented.

------------------------------------------------------------------------

# 4. Application Structure Plan

The application should follow the defined Next.js structure.

``` text
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

Modules should follow the architectural layering:

``` text
Presentation
      ↓
Application
      ↓
Domain
      ↓
Infrastructure
```

The transaction module is the defined example for module organization:

``` text
transactions/

transaction.controller.ts
transaction.service.ts
transaction.repository.ts
transaction.schema.ts
transaction.types.ts
```

The same architectural separation should guide the implementation of the
other defined modules without introducing additional domain
capabilities.

------------------------------------------------------------------------

# 5. Database Foundation Plan

## 5.1 Core Tables

The initial database design must cover the core tables defined in the
architecture:

``` text
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

Later-stage tables already identified by the architecture are:

``` text
bank_connections
bank_transactions
merchants
subscriptions
households
household_members
net_worth_snapshots
```

These later tables should be introduced only when their corresponding
roadmap capabilities are developed.

## 5.2 Core Relationships

The database implementation should preserve this model:

``` text
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

## 5.3 Financial Data Handling

Financial values must not depend on JavaScript floating-point
arithmetic.

The approved PostgreSQL representation is:

``` sql
amount NUMERIC(19,4) NOT NULL
```

Financial calculations should use decimal-safe logic rather than relying
directly on:

``` typescript
number
```

## 5.4 Multi-Currency Foundation

Multi-currency support must be considered from the beginning.

The defined fields are:

``` text
amount
currency
exchange_rate
base_amount
base_currency
```

The architecture provides the following example:

``` text
amount         100
currency       USD

exchange_rate  58.25

base_amount    5825
base_currency  PHP
```

## 5.5 Transaction Indexes

The following indexes are already defined and should be included:

``` sql
CREATE INDEX idx_transactions_user_date
ON transactions(user_id, transaction_date DESC);

CREATE INDEX idx_transactions_account
ON transactions(account_id);

CREATE INDEX idx_transactions_category
ON transactions(category_id);

CREATE INDEX idx_transactions_user_type
ON transactions(user_id, type);
```

A critical query pattern is:

``` text
Get this user's transactions within a date range.
```

------------------------------------------------------------------------

# 6. Security Foundation Plan

Security must be implemented as part of the application foundation
rather than treated as a later feature.

Required controls:

-   HTTPS
-   Secure session cookies
-   CSRF protection where applicable
-   Input validation
-   Rate limiting
-   Authorization
-   Encryption at rest
-   Encrypted backups
-   Audit logs
-   Database least privilege
-   Security headers
-   Secret management

All resource access must be user scoped.

Critical authorization rule:

``` text
resource.user_id === authenticatedUser.id
```

The application must never trust a `userId` received directly from the
client for authorization.

The authenticated user must come from the server-side session.

------------------------------------------------------------------------

# 7. Authentication Development Plan

Authentication uses **Auth.js**.

Potential providers already defined are:

-   Email/password
-   Google
-   Apple

Authentication flow:

``` text
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

Development work for authentication must establish protected application
access and provide the authenticated server-side user context required
by all user-scoped resources.

------------------------------------------------------------------------

# 8. Validation Plan

Request validation uses **Zod**.

The transaction creation model defined in the architecture is:

``` typescript
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

Validation should be applied at application boundaries for the defined
APIs and business operations.

------------------------------------------------------------------------

# 9. Phase 1 --- Core MVP Development Plan

Phase 1 contains:

-   Authentication
-   Accounts
-   Transactions
-   Categories
-   Tags
-   Dashboard
-   Monthly budgets
-   Reports
-   Recurring expenses
-   Savings goals

The Phase 1 goal is:

> A user can completely track and understand personal finances manually.

## 9.1 Authentication

### Development scope

Implement the Auth.js authentication flow and protect the application
using sessions and Next.js middleware.

### Required result

Authenticated users can access the protected application, while
application data access uses the authenticated server-side user rather
than a client-supplied user identifier.

------------------------------------------------------------------------

## 9.2 Accounts and Wallets

Each transaction must belong to an account or wallet.

Supported account types:

``` text
CASH
BANK
EWALLET
CREDIT_CARD
SAVINGS
INVESTMENT
LOAN
OTHER
```

### Development scope

Implement the account domain and its relationship with users and
transactions.

The account experience must support the account/wallet concept
represented by examples such as:

``` text
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

### Required result

Transactions can be associated with the correct account or wallet rather
than being attached only to a user.

------------------------------------------------------------------------

## 9.3 Categories

Categories must support hierarchy.

Defined example:

``` text
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

Defined category structure:

``` text
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

### Development scope

Implement categories with `parent_id` support so parent and child
categories can be represented.

### Required result

Transactions can be classified using the hierarchical category model.

------------------------------------------------------------------------

## 9.4 Tags

Tags must remain independent from categories.

Examples:

``` text
#vacation
#work
#family
#business
#reimbursement
#japan-trip
```

Defined use case:

``` text
Show all expenses tagged #japan-trip
```

### Development scope

Implement `tags` and `transaction_tags` so a transaction can be
associated with tags independently from its category.

### Required result

Transactions can be grouped and queried through tags without changing
their normal spending category.

------------------------------------------------------------------------

## 9.5 Transactions

Transactions are the core domain object.

Defined transaction model:

``` typescript
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

Defined transaction sources:

``` text
MANUAL
IMPORT
BANK_SYNC
RECURRING
SYSTEM
```

### Development scope

Implement support for:

-   Expense transactions
-   Income transactions
-   Transfer transactions
-   Account association
-   Category association
-   Tag association
-   Transaction source
-   Transaction date
-   Merchant
-   Description
-   Currency and financial amount handling

### Transfer requirement

A transfer must not be counted as an expense.

Example:

``` text
BDO
- ₱5,000

GCash
+ ₱5,000
```

Transfer entries must be linked through a transfer reference using the
defined `transaction_transfers` concept.

### API scope

Implement the defined transaction endpoints:

``` http
POST /api/v1/transactions

GET /api/v1/transactions

GET /api/v1/transactions/:id

PATCH /api/v1/transactions/:id

DELETE /api/v1/transactions/:id
```

Filtering must support the defined query structure:

``` http
GET /api/v1/transactions
    ?from=2026-08-01
    &to=2026-08-31
    &category=food
    &account=bdo
    &type=expense
    &page=1
    &limit=50
```

### Required result

Users can manually manage expense, income, and transfer transactions and
retrieve transactions using the defined filtering model.

------------------------------------------------------------------------

## 9.6 Monthly Budgets

The budget engine must support monthly category budgets.

Example:

``` text
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

Budget progress must represent:

``` text
Budget
₱12,000

Spent
₱9,400

Remaining
₱2,600

Usage
78%
```

Defined alert thresholds:

``` text
50%
75%
90%
100%
```

### Development scope

Implement `budgets` and `budget_categories`.

Implement the defined APIs:

``` http
POST /api/v1/budgets

GET /api/v1/budgets/current

GET /api/v1/budgets/:id/progress
```

### Required result

Users can define monthly budgets and view budget, spending, remaining
amount, and usage progress.

------------------------------------------------------------------------

## 9.7 Recurring Expenses

Supported frequencies:

``` text
DAILY
WEEKLY
BIWEEKLY
MONTHLY
QUARTERLY
YEARLY
CUSTOM
```

Defined recurring expense example:

``` text
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

### Development scope

Implement the `recurring_transactions` domain for the supported
frequencies and defined recurring transaction information.

### Required result

Users can represent recurring expenses and see upcoming recurring
financial obligations.

The architecture identifies automatic subscription detection as a later
capability, so it is not part of the initial recurring-expense
implementation.

------------------------------------------------------------------------

## 9.8 Savings Goals

Defined goal examples:

``` text
Emergency Fund
Vacation
Car
House
Laptop
Wedding
Education
Investment
```

Defined goal structure:

``` text
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

The core database also defines:

``` text
goal_contributions
```

### Development scope

Implement savings goals using the defined target amount, current amount,
target date, and status.

### Required result

Users can monitor goal progress such as:

``` text
Emergency Fund

Goal
₱150,000

Saved
₱83,500

Progress
56%
```

------------------------------------------------------------------------

## 9.9 Dashboard

The dashboard must surface the financial summary defined in the
architecture.

Required summary information:

-   Total Balance
-   Income
-   Expenses
-   Available amount
-   Remaining Budget
-   Upcoming Bills
-   Savings Progress
-   Spending by Category
-   Monthly Budget progress

Defined dashboard API:

``` http
GET /api/v1/dashboard/summary
```

Defined response example:

``` json
{
  "balance": 156450,
  "income": 95000,
  "expenses": 52340,
  "savings": 42660,
  "budgetUsedPercentage": 74.77
}
```

### Development scope

Implement dashboard aggregation using PostgreSQL rather than calculating
all analytics in frontend JavaScript.

The architecture defines the following aggregation pattern:

``` sql
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

### Required result

Users can see a consolidated financial overview and spending
distribution for the relevant period.

------------------------------------------------------------------------

## 9.10 Reports

Initial reports are limited to the reports already defined:

-   Spending by Category
-   Income vs Expenses
-   Monthly Spending Trend
-   Budget vs Actual
-   Cash Flow
-   Top Merchants
-   Recurring Expenses
-   Savings Rate

### Development scope

Implement the report module and use PostgreSQL aggregations for report
calculations.

### Required result

Users can analyze the financial information already captured by
accounts, transactions, budgets, recurring expenses, and goals.

------------------------------------------------------------------------

## 9.11 User Settings

User Settings is part of the MVP scope, and `user_preferences` is
included in the core database tables.

### Development scope

Create the settings application area and connect it to the defined
`user_preferences` data concept.

### Scope constraint

The source architecture does not define individual preference fields.
This plan therefore does not introduce additional settings fields.

------------------------------------------------------------------------

## 9.12 Audit Logging

Audit logs are a core table and a minimum security requirement.

Defined structure:

``` text
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

``` text
TRANSACTION_UPDATED

Previous
₱500 Food

New
₱5,000 Food
```

Audit logs should generally be append-only.

### Development scope

Implement audit logging for applicable financial data changes using the
defined audit structure.

------------------------------------------------------------------------

## 9.13 Observability

Production requirements already defined are:

-   Application logs
-   Error tracking
-   Performance monitoring
-   Database metrics
-   Audit logs
-   Request correlation IDs

Recommended log context:

``` text
requestId
userId
endpoint
duration
status
```

Never log:

-   Passwords
-   Session tokens
-   Bank credentials
-   Sensitive financial secrets

Sentry is the defined error-monitoring technology.

------------------------------------------------------------------------

## 9.14 Testing

The approved testing stack is:

``` text
Vitest + Playwright
```

Testing should cover the implemented application behavior defined by the
MVP modules.

No additional testing frameworks are introduced by this plan.

------------------------------------------------------------------------

## 9.15 CI/CD and Initial Deployment

The approved CI/CD technology is:

``` text
GitHub Actions
```

The initial deployment architecture is:

``` text
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

The source architecture identifies Vercel for application hosting and
Neon, Supabase, or AWS RDS as database-hosting options.

This plan does not select one of the three database-hosting options
because the source architecture does not make that selection.

------------------------------------------------------------------------

# 10. Phase 1 Completion Criteria

Phase 1 is complete when the implemented product satisfies the defined
MVP objective:

> A user can completely track and understand personal finances manually.

The delivered capabilities must include:

-   Authentication
-   Accounts and wallets
-   Expense, income, and transfer transactions
-   Hierarchical categories
-   Independent transaction tags
-   Monthly budgets and progress
-   Recurring expenses
-   Savings goals
-   Dashboard financial summary
-   Initial reports
-   User Settings area connected to the defined user-preferences concept
-   User-scoped authorization
-   Financial-safe data handling
-   Audit logging
-   Defined observability
-   Vitest and Playwright testing
-   GitHub Actions CI/CD
-   Deployment using the approved initial architecture

------------------------------------------------------------------------

# 11. Phase 2 --- Smart Personal Finance Development Plan

Phase 2 is limited to:

-   Shared wallets
-   Subscriptions
-   Advanced reporting
-   CSV imports
-   Notifications
-   Financial-health score
-   Net worth
-   Forecasting

No other Phase 2 capabilities are added by this plan.

## 11.1 Shared Wallets

The database roadmap already identifies:

``` text
households
household_members
```

### Development direction

Implement the shared-wallet capability using the future
household-related data concepts already identified by the architecture.

The source does not define detailed household permissions or sharing
rules, so this plan does not introduce them.

------------------------------------------------------------------------

## 11.2 Subscriptions

The database roadmap identifies:

``` text
subscriptions
```

The recurring-expense module is also identified as a foundation that can
later support automatic subscription detection.

### Development direction

Introduce subscription functionality as defined by Phase 2.

The source does not define a detailed subscription schema, so no
additional fields are introduced in this plan.

------------------------------------------------------------------------

## 11.3 Advanced Reporting

Phase 2 introduces advanced reporting.

The later reports explicitly identified are:

-   Net Worth
-   Debt Ratio
-   Emergency Fund Coverage
-   Average Daily Spend
-   Projected Month-End Balance

### Development direction

Extend the existing report module with the later report capabilities
defined by the architecture.

------------------------------------------------------------------------

## 11.4 CSV Imports

CSV imports are explicitly listed for Phase 2.

The transaction model already supports:

``` text
IMPORT
```

as a transaction source.

### Development direction

Introduce CSV imports into the transaction flow while preserving the
existing transaction model and source classification.

The architecture does not define a CSV file schema or column mapping, so
this plan does not create one.

------------------------------------------------------------------------

## 11.5 Notifications

`notifications` is already included in the core database table list.

Phase 2 explicitly introduces notifications.

### Development direction

Enable the notification capability for the financial application.

The source architecture does not define notification channels or
notification schemas, so this plan does not add them.

------------------------------------------------------------------------

## 11.6 Financial Health Score

The financial-health score is a defined differentiating feature.

Example:

``` text
FINANCIAL HEALTH SCORE

78 / 100
GOOD
```

Possible factors already defined:

-   Savings rate
-   Budget discipline
-   Debt ratio
-   Emergency fund coverage
-   Recurring cost ratio
-   Income-to-expense ratio

Example presentation:

``` text
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

### Development direction

Implement the score using only the factors identified in the
architecture.

The source does not define a scoring formula or factor weights, so this
plan does not invent them.

------------------------------------------------------------------------

## 11.7 Net Worth

Net worth is a Phase 2 capability.

The future database tables include:

``` text
net_worth_snapshots
```

### Development direction

Introduce net-worth tracking and the corresponding later report.

The source does not define the snapshot schema or calculation formula,
so this plan does not introduce additional rules.

------------------------------------------------------------------------

## 11.8 Forecasting

The forecasting engine is defined using:

``` text
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

``` text
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

### Development direction

Implement forecasting around the defined month-end projection model.

The architecture does not specify a statistical forecasting algorithm,
so none is introduced here.

------------------------------------------------------------------------

# 12. Phase 3 --- Automation Development Plan

Phase 3 is limited to:

-   Bank synchronization
-   Automatic transaction import
-   Automatic categorization
-   Recurring-payment detection
-   Duplicate detection
-   Merchant normalization

## 12.1 Bank Synchronization

Bank synchronization follows the defined architecture:

``` text
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

Future database concepts already identified:

``` text
bank_connections
bank_transactions
merchants
```

### Development direction

Implement bank synchronization through the defined financial-aggregator
flow.

The source architecture does not identify a specific financial
aggregator, so this plan does not select one.

------------------------------------------------------------------------

## 12.2 Automatic Transaction Import

The transaction model already includes:

``` text
BANK_SYNC
```

as a source.

### Development direction

Normalize imported bank data into the existing transaction model.

The transaction schema must support imported data from day one, so Phase
1 database design must not assume that all transactions are manually
created.

------------------------------------------------------------------------

## 12.3 Automatic Categorization

Phase 3 introduces automatic categorization.

The architecture gives these examples:

``` text
Starbucks
→ Food / Coffee

Shell
→ Transportation / Fuel

Meralco
→ Utilities / Electricity
```

### Development direction

Use the existing hierarchical category model as the destination for
automatically categorized transactions.

No categorization algorithm is specified in the source and therefore
none is introduced by this plan.

------------------------------------------------------------------------

## 12.4 Recurring-Payment Detection

Recurring-payment detection is explicitly part of Phase 3.

### Development direction

Build on the existing recurring-expense and subscription concepts to
detect recurring payments.

The source does not define the detection rules, so this plan does not
add them.

------------------------------------------------------------------------

## 12.5 Duplicate Detection

Duplicate detection is explicitly part of Phase 3.

### Development direction

Apply duplicate detection to the automated transaction-import flow.

The source does not define duplicate-matching rules, so no matching
algorithm is introduced here.

------------------------------------------------------------------------

## 12.6 Merchant Normalization

Merchant normalization is explicitly part of Phase 3.

The future database model already identifies:

``` text
merchants
```

### Development direction

Normalize merchant information before or as imported transactions become
application transactions, consistent with the defined bank-integration
flow.

The source does not define normalization rules, so none are added.

------------------------------------------------------------------------

# 13. Phase 4 --- Intelligent Finance Development Plan

Phase 4 is limited to:

-   AI insights
-   Natural-language queries
-   Spending predictions
-   Expense anomaly detection
-   Budget recommendations
-   Savings recommendations

AI must not be part of the first MVP.

## 13.1 AI Insights

The architecture defines spending insights such as:

``` text
Your food spending increased 31%
compared with last month.
```

### Development direction

Use existing financial and analytics data to provide AI insights.

No AI provider or model is specified in the architecture, so this plan
does not select one.

------------------------------------------------------------------------

## 13.2 Natural-Language Queries

Defined example:

``` text
How much did I spend eating outside during the last three months?
```

The architecture states that the system converts the request into
structured analytics filters.

### Development direction

Connect natural-language requests to the existing analytics and
transaction-filtering capabilities.

------------------------------------------------------------------------

## 13.3 Spending Predictions

Spending predictions are explicitly listed for Phase 4.

### Development direction

Add spending predictions to the intelligent-finance layer.

The architecture does not define a prediction algorithm, so none is
introduced.

------------------------------------------------------------------------

## 13.4 Expense Anomaly Detection

Defined example:

``` text
This ₱18,500 transaction is significantly
higher than your normal shopping expenses.
```

### Development direction

Use transaction history to surface expense anomalies.

The architecture does not define thresholds or anomaly-detection
formulas, so this plan does not add them.

------------------------------------------------------------------------

## 13.5 Budget Recommendations

Budget recommendations are explicitly listed for Phase 4.

### Development direction

Use the existing budget and analytics domains as the basis for
recommendations.

The source does not define recommendation formulas.

------------------------------------------------------------------------

## 13.6 Savings Recommendations

Savings recommendations are explicitly listed for Phase 4.

### Development direction

Use the existing savings-goal and financial analytics data as the basis
for recommendations.

The source does not define recommendation formulas.

------------------------------------------------------------------------

# 14. Background Jobs Plan

Background jobs are a later architecture capability.

Future asynchronous workloads already defined are:

-   Recurring transaction generation
-   Budget notifications
-   Monthly reports
-   Subscription detection
-   Bank synchronization
-   Email notifications
-   Financial insights

Defined architecture:

``` text
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

Possible implementation already specified:

``` text
BullMQ + Redis
```

Background-job infrastructure should therefore be introduced when these
later asynchronous workloads are implemented, not as unnecessary initial
complexity.

------------------------------------------------------------------------

# 15. Caching Plan

Redis must not be introduced immediately.

Initial architecture:

``` text
Next.js
    ↓
PostgreSQL
```

Later architecture:

``` text
Next.js
    │
    ├── Redis
    │
    └── PostgreSQL
```

Defined possible Redis uses:

-   Rate limiting
-   Dashboard caching
-   Sessions
-   Distributed locks
-   Background-job coordination

Caching should be introduced only when the later architecture requires
it.

------------------------------------------------------------------------

# 16. Analytics Optimization Plan

Analytics should initially be performed through PostgreSQL aggregation.

Later optimization options already identified are:

-   Indexes
-   Materialized views
-   Summary tables
-   Redis caching

These optimizations should be introduced only as needed rather than
being added to the initial implementation without demonstrated need.

------------------------------------------------------------------------

# 17. Later Deployment Evolution

The later deployment architecture defined by the source is:

``` text
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

This is an evolution of the initial Vercel + PostgreSQL architecture and
should be considered when the later Redis and background-job
capabilities are introduced.

------------------------------------------------------------------------

# 18. Development Dependency Order

The following dependency order is derived from the relationships and
phases already defined in the architecture.

## Foundation

``` text
Architecture
   ↓
Next.js application structure
   ↓
PostgreSQL / Drizzle foundation
   ↓
Authentication and user-scoped security
   ↓
Validation
```

## Core Financial Domain

``` text
Users
   ↓
Accounts
   ↓
Categories + Tags
   ↓
Transactions
   ↓
Transfers
```

## Planning Domain

``` text
Transactions
   ↓
Budgets
   ↓
Recurring Expenses
   ↓
Savings Goals
```

## Analytics and Presentation

``` text
Accounts + Transactions + Budgets + Recurring Expenses + Goals
   ↓
PostgreSQL Aggregations
   ↓
Dashboard
   ↓
Reports
```

## Later Evolution

``` text
Phase 1 Core MVP
   ↓
Phase 2 Smart Personal Finance
   ↓
Phase 3 Automation
   ↓
Phase 4 Intelligent Finance
```

This dependency order does not replace the approved roadmap; it
organizes the already-defined capabilities according to their stated
relationships.

------------------------------------------------------------------------

# 19. Scope Control

Development should remain aligned with the approved architecture.

The following details are intentionally not specified in this plan
because they are not defined in the source document:

-   Sprint length
-   Number of sprints
-   Calendar estimates
-   Story-point estimates
-   Team size
-   Team roles
-   Specific database-hosting choice among Neon, Supabase, or AWS RDS
-   Specific financial aggregator
-   Detailed user-preference fields
-   Household permission model
-   Notification channels
-   CSV column format
-   Subscription schema
-   Financial-health scoring weights
-   Net-worth calculation rules
-   Forecasting algorithm
-   Automatic-categorization algorithm
-   Duplicate-detection algorithm
-   Merchant-normalization rules
-   AI provider or model
-   Spending-prediction algorithm
-   Anomaly thresholds
-   Budget-recommendation formula
-   Savings-recommendation formula

These items require separate decisions or additional requirements before
they can be added to the development plan.

------------------------------------------------------------------------

# 20. Final Delivery Sequence

The approved development sequence is:

## Phase 1 --- Core MVP

``` text
Authentication
Accounts
Transactions
Categories
Tags
Dashboard
Monthly budgets
Reports
Recurring expenses
Savings goals
```

Goal:

> A user can completely track and understand personal finances manually.

## Phase 2 --- Smart Personal Finance

``` text
Shared wallets
Subscriptions
Advanced reporting
CSV imports
Notifications
Financial-health score
Net worth
Forecasting
```

## Phase 3 --- Automation

``` text
Bank synchronization
Automatic transaction import
Automatic categorization
Recurring-payment detection
Duplicate detection
Merchant normalization
```

## Phase 4 --- Intelligent Finance

``` text
AI insights
Natural-language queries
Spending predictions
Expense anomaly detection
Budget recommendations
Savings recommendations
```

The implementation should preserve the product progression:

``` text
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

The final product direction remains:

> **Personal Financial Command Center**
