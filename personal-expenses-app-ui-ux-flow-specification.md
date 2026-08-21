# Personal Expenses App --- UI/UX Design & User Flow Specification

## 1. Purpose

This document defines the UI/UX direction, navigation flow, page
structure, responsive behavior, and reusable interface patterns for the
**Personal Expenses App**.

The design remains aligned with the approved product direction:

> **Personal Financial Command Center**

The experience should help users move through the established product
journey:

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

For the initial version, the UI focuses on the approved MVP modules:

1.  Authentication
2.  Dashboard
3.  Accounts / Wallets
4.  Transactions
5.  Categories
6.  Tags
7.  Budgets
8.  Recurring Expenses
9.  Savings Goals
10. Reports
11. User Settings

The primary UX objective is to make personal financial information
understandable without making the application feel like an accounting
system.

------------------------------------------------------------------------

# 2. Design Direction

The interface should use a modern financial-dashboard approach.

Primary characteristics:

-   Clean
-   Spacious
-   Data-focused
-   Easy to scan
-   Strong monetary-value hierarchy
-   Minimal visual noise
-   Clear financial status
-   Consistent interaction patterns
-   Responsive across desktop, tablet, and mobile

The approved UI technologies are:

``` text
Next.js + TypeScript
Tailwind CSS
shadcn/ui
Recharts
```

------------------------------------------------------------------------

# 3. Information Hierarchy

Financial information should follow this visual hierarchy:

``` text
Financial Number
      ↓
Meaning / Label
      ↓
Status / Progress
      ↓
Supporting Information
      ↓
Action
```

Example:

``` text
₱52,340
Expenses

75% of monthly budget used

₱17,660 remaining
```

Important monetary values should be visually stronger than labels and
secondary information.

------------------------------------------------------------------------

# 4. Primary Navigation Architecture

The desktop application uses persistent primary navigation.

``` text
Dashboard
Accounts
Transactions
Budgets
Goals
Recurring
Reports
Settings
```

Categories and tags primarily support transaction organization rather
than requiring primary navigation entries.

------------------------------------------------------------------------

# 5. Main Application Flow

``` text
                    ┌───────────────┐
                    │     LOGIN     │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │   DASHBOARD   │
                    └───────┬───────┘
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
          ▼                 ▼                  ▼
      ACCOUNTS         TRANSACTIONS         BUDGETS
          │                 │                  │
          │          ┌──────┼──────┐           │
          │          ▼      ▼      ▼           │
          │       EXPENSE INCOME TRANSFER      │
          │                                    │
          ├──────────────┐                     │
          │              │                     │
          ▼              ▼                     ▼
        GOALS       RECURRING BILLS          REPORTS
          │              │                     │
          └──────────────┼─────────────────────┘
                         │
                         ▼
                      SETTINGS
```

------------------------------------------------------------------------

# 6. Product Experience Flow

The overall experience should not feel like disconnected CRUD screens.

``` text
                         LOGIN
                           │
                           ▼
                       DASHBOARD
                           │
              ┌────────────┼─────────────┐
              ▼            ▼             ▼
           ACCOUNTS   TRANSACTIONS     BUDGET
                           │             │
                     ┌─────┼─────┐       │
                     ▼     ▼     ▼       ▼
                  EXPENSE INCOME TRANSFER
                           │             │
                           └──────┬──────┘
                                  ▼
                              UNDERSTAND
                                  │
                   ┌──────────────┼──────────────┐
                   ▼              ▼              ▼
                REPORTS       RECURRING        GOALS
                   │                              │
                   └──────────────┬───────────────┘
                                  ▼
                                PLAN
                                  │
                                  ▼
                              PHASE 2+
                                  │
                          ┌───────┴────────┐
                          ▼                ▼
                      FORECAST       FINANCIAL
                                      HEALTH
                          │                │
                          └───────┬────────┘
                                  ▼
                               IMPROVE
```

------------------------------------------------------------------------

# 7. Desktop Application Shell

The desktop interface should use a persistent sidebar and a large main
content area.

``` text
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                            │
│  ◉ FINANCE        │  Dashboard                         August 2026      ◉   │
│                   │                                                        │
│  ▣ Overview       │  Good afternoon                                       │
│                   │  Here's your financial overview.                       │
│  ◫ Accounts       │                                                        │
│  ⇄ Transactions   │  ┌──────────────────────────────────────────────────┐  │
│  ◉ Budgets        │  │ TOTAL BALANCE                                    │  │
│  ◎ Goals          │  │                                                  │  │
│  ◷ Recurring      │  │ ₱156,450                                        │  │
│  ▤ Reports        │  │                                                  │  │
│                   │  │ + Income ₱95,000      - Expenses ₱52,340         │  │
│                   │  └──────────────────────────────────────────────────┘  │
│                   │                                                        │
│                   │  ┌────────────────────┐ ┌───────────────────────────┐ │
│                   │  │ Monthly Budget     │ │ Spending by Category      │ │
│                   │  │                    │ │                           │ │
│                   │  │ ₱52,340            │ │        DONUT CHART        │ │
│                   │  │ of ₱70,000         │ │                           │ │
│                   │  │ ███████████░ 75%   │ │ Food          24%        │ │
│                   │  │                    │ │ Housing       29%        │ │
│                   │  │ ₱17,660 remaining  │ │ Transport     12%        │ │
│                   │  └────────────────────┘ └───────────────────────────┘ │
│                   │                                                        │
│  ⚙ Settings       │  Recent Transactions                                  │
│                   │  ┌──────────────────────────────────────────────────┐  │
│                   │  │ Starbucks      Food       -₱250      Today      │  │
│                   │  │ Grab           Transport  -₱340      Today      │  │
│                   │  │ Salary         Income    +₱45,000    Aug 15     │  │
│                   │  └──────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────┘
```

------------------------------------------------------------------------

# 8. Sidebar Navigation

The sidebar should provide predictable access to the primary financial
areas.

``` text
FINANCE

Overview
Accounts
Transactions
Budgets
Goals
Recurring
Reports

Settings
```

The current location should always be visually identifiable.

The sidebar should remain visually secondary to the financial content.

------------------------------------------------------------------------

# 9. Authentication Flow

Authentication precedes access to the protected application.

``` text
LOGIN
  │
  ▼
AUTHENTICATED SESSION
  │
  ▼
PROTECTED APPLICATION
  │
  ▼
DASHBOARD
```

The application architecture already defines:

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

The authentication UI should remain focused and avoid exposing financial
dashboard complexity before login.

------------------------------------------------------------------------

# 10. Dashboard UX

The dashboard is the financial overview of the application.

It should immediately communicate:

-   Total Balance
-   Income
-   Expenses
-   Available amount
-   Remaining Budget
-   Upcoming Bills
-   Savings Progress
-   Spending by Category
-   Monthly Budget progress
-   Recent Transactions

## 10.1 Dashboard Information Flow

``` text
DASHBOARD
   │
   ├── Financial Summary
   │      ├── Total Balance
   │      ├── Income
   │      ├── Expenses
   │      └── Available
   │
   ├── Monthly Budget
   │      ├── Budget
   │      ├── Spent
   │      ├── Remaining
   │      └── Usage %
   │
   ├── Spending by Category
   │
   ├── Upcoming Bills
   │
   ├── Savings Progress
   │
   └── Recent Transactions
```

## 10.2 Financial Summary

Example:

``` text
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

The total balance should have the strongest hierarchy.

Income and expenses provide supporting context.

## 10.3 Spending by Category

Example:

``` text
Food             ₱12,300  24%
Housing          ₱15,000  29%
Transportation    ₱6,400  12%
Shopping          ₱5,500  11%
Utilities         ₱4,300   8%
Others            ₱8,840  16%
```

Recharts can represent this information visually while preserving the
category values alongside the visualization.

## 10.4 Monthly Budget Card

``` text
Monthly Budget

₱52,340 / ₱70,000

████████████████░░░░

75% used

₱17,660 remaining
```

The budget card should communicate status before requiring detailed
inspection.

------------------------------------------------------------------------

# 11. Accounts UX

Each transaction belongs to an account or wallet.

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

## 11.1 Accounts Overview

``` text
Accounts

┌───────────────────┐ ┌───────────────────┐
│ BDO Savings       │ │ BPI               │
│ BANK              │ │ BANK              │
│                   │ │                   │
│ ₱84,000           │ │ ₱32,500           │
└───────────────────┘ └───────────────────┘

┌───────────────────┐ ┌───────────────────┐
│ GCash             │ │ Cash              │
│ EWALLET           │ │ CASH              │
│                   │ │                   │
│ ₱11,350           │ │ ₱5,600            │
└───────────────────┘ └───────────────────┘

┌───────────────────┐
│ Credit Card       │
│ CREDIT CARD       │
│                   │
│ -₱8,400           │
└───────────────────┘
```

Each account card should prioritize:

``` text
Account Name
Account Type
Balance
```

------------------------------------------------------------------------

# 12. Transactions UX

Transactions are the central operational screen.

Supported transaction types:

``` text
EXPENSE
INCOME
TRANSFER
```

Supported transaction sources:

``` text
MANUAL
IMPORT
BANK_SYNC
RECURRING
SYSTEM
```

## 12.1 Transaction List

``` text
Transactions                                      + Add Transaction

┌─────────────────────────────────────────────────────────────────────┐
│ Search transactions...                                             │
│                                                                     │
│ [All Accounts ▼] [All Categories ▼] [Type ▼] [Date ▼]             │
├─────────────────────────────────────────────────────────────────────┤
│ TODAY                                                               │
│                                                                     │
│ ● Starbucks        Food / Coffee        BDO             - ₱250     │
│ ● Grab             Transportation       GCash           - ₱340     │
│                                                                     │
│ AUGUST 20                                                          │
│                                                                     │
│ ● Salary           Income               BPI          + ₱45,000     │
│ ● Netflix          Entertainment        BDO             - ₱549     │
└─────────────────────────────────────────────────────────────────────┘
```

The list should support the already-defined filtering model:

``` text
Date range
Category
Account
Transaction type
Pagination
```

## 12.2 Transaction Row Hierarchy

A transaction row should communicate:

``` text
Merchant / Description
Category
Account
Amount
Date
```

The amount should be easy to scan.

Income and expense values must remain distinguishable in meaning.

------------------------------------------------------------------------

# 13. Add Transaction Flow

The Add Transaction interaction should be highly accessible because
manual transaction management is the first V1 priority.

``` text
TRANSACTIONS
     │
     ▼
+ ADD TRANSACTION
     │
     ├── EXPENSE
     ├── INCOME
     └── TRANSFER
```

## 13.1 Transaction Form

``` text
              Add Transaction
┌─────────────────────────────────────┐
│                                     │
│  [ Expense ] [ Income ] [ Transfer ]│
│                                     │
│             ₱ 0.00                  │
│                                     │
│  Account                            │
│  [ BDO Savings                  ▼ ] │
│                                     │
│  Category                           │
│  [ Food / Restaurant           ▼ ] │
│                                     │
│  Date                               │
│  [ August 21, 2026              ]  │
│                                     │
│  Merchant                           │
│  [                              ]  │
│                                     │
│  Description                        │
│  [                              ]  │
│                                     │
│  Tags                               │
│  [ #work ] [ #reimbursement ]      │
│                                     │
│            [ Save Transaction ]     │
└─────────────────────────────────────┘
```

The amount is the primary input and should receive the strongest visual
emphasis.

The transaction form reflects the defined transaction domain:

``` text
Account
Type
Amount
Currency
Category
Merchant
Description
Transaction Date
Source
Tags
```

------------------------------------------------------------------------

# 14. Transfer UX

A transfer must not be presented as an expense.

Defined behavior:

``` text
BDO
- ₱5,000

GCash
+ ₱5,000
```

The interface should communicate movement between accounts rather than
spending.

``` text
TRANSFER

From
BDO Savings

Amount
₱5,000

To
GCash
```

The underlying entries remain linked through the defined transfer
reference.

------------------------------------------------------------------------

# 15. Categories UX

Categories are hierarchical.

Example:

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

The UI should make parent-child relationships understandable when
selecting or viewing categories.

Example selection:

``` text
Category

Food
  Groceries
  Restaurant
  Coffee
  Delivery

Transportation
  Fuel
  Taxi
  Grab
  Public Transport
```

------------------------------------------------------------------------

# 16. Tags UX

Tags remain independent from categories.

Examples:

``` text
#vacation
#work
#family
#business
#reimbursement
#japan-trip
```

Transaction presentation can show tags as compact supporting metadata.

Example:

``` text
Starbucks

Food / Coffee

#work
#reimbursement

-₱250
```

The defined tag use case should remain possible:

``` text
Show all expenses tagged #japan-trip
```

------------------------------------------------------------------------

# 17. Budget UX

The budget screen should communicate current status quickly.

## 17.1 Budget Overview

``` text
August Budget

₱52,340
spent of ₱70,000

████████████████████████████░░░░░░░

75% used                         ₱17,660 left
```

## 17.2 Category Budget List

``` text
CATEGORY                 SPENT              BUDGET

Food
██████████████░░          ₱9,400             ₱12,000
78%

Transport
█████████░░░░░░░          ₱4,500              ₱8,000
56%

Entertainment
████████████████░          ₱3,600              ₱4,000
90%

Shopping
██████░░░░░░░░░░          ₱1,900              ₱5,000
38%
```

## 17.3 Budget Status Thresholds

The approved budget thresholds are:

``` text
50%
75%
90%
100%
```

The UI should clearly communicate these status changes without requiring
the user to manually calculate budget usage.

------------------------------------------------------------------------

# 18. Recurring Expenses UX

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

## 18.1 Recurring Expenses List

``` text
Recurring Expenses

Monthly recurring expenses
₱3,597

┌─────────────────────────────────────────────────────┐
│ Netflix                                      ₱549   │
│ Entertainment                Monthly                │
│ Next payment • Aug 25                               │
├─────────────────────────────────────────────────────┤
│ Spotify                                      ₱149   │
│ Entertainment                Monthly                │
├─────────────────────────────────────────────────────┤
│ Internet                                   ₱1,699   │
│ Utilities                    Monthly                │
└─────────────────────────────────────────────────────┘
```

Each recurring item should emphasize:

``` text
Name
Amount
Category
Frequency
Next Payment
```

Automatic subscription detection is a later capability and should not be
represented as an MVP feature.

------------------------------------------------------------------------

# 19. Savings Goals UX

Goals should emphasize progress.

Examples include:

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

## 19.1 Goal Cards

``` text
Savings Goals                                  + New Goal

Emergency Fund

₱83,500
of ₱150,000

██████████████████░░░░░░░░░░░░

56%

Target
₱150,000


Vacation

₱25,000
of ₱80,000

█████████░░░░░░░░░░░░░░░░░░░░

31%
```

Goal presentation should communicate:

``` text
Goal Name
Current Amount
Target Amount
Progress
Target Date
Status
```

------------------------------------------------------------------------

# 20. Reports UX

Initial reports are limited to:

-   Spending by Category
-   Income vs Expenses
-   Monthly Spending Trend
-   Budget vs Actual
-   Cash Flow
-   Top Merchants
-   Recurring Expenses
-   Savings Rate

## 20.1 Reports Layout

``` text
Reports

[ This Month ▼ ]

┌────────────────────────────────────────────────────┐
│ Income vs Expenses                                 │
│                                                    │
│                 TREND CHART                        │
│                                                    │
└────────────────────────────────────────────────────┘

┌────────────────────────┐ ┌─────────────────────────┐
│ Spending by Category   │ │ Savings Rate            │
│                        │ │                         │
│      DONUT CHART       │ │        21%              │
│                        │ │                         │
└────────────────────────┘ └─────────────────────────┘

Top Merchants

Starbucks                              ₱3,450
Grab                                   ₱3,210
...
```

The interface should avoid displaying every possible visualization
simultaneously.

Reports should be organized so users can understand the most relevant
information first and inspect supporting data afterward.

------------------------------------------------------------------------

# 21. Settings UX

Settings is part of the MVP and connects to the defined
`user_preferences` concept.

``` text
Settings
```

The approved architecture does not define individual preference fields.

Therefore this UI/UX specification does not introduce additional
settings fields.

------------------------------------------------------------------------

# 22. Reusable UI Component Inventory

The following component categories are derived from the approved MVP
screens and existing data presentation requirements.

## 22.1 Navigation

``` text
Sidebar
Navigation Item
Mobile Navigation
Page Header
```

## 22.2 Financial Display

``` text
Financial Summary Card
Account Card
Budget Progress
Goal Progress
Recurring Expense Row
Transaction Row
Report Card
Chart Container
```

## 22.3 Transaction Interface

``` text
Transaction Type Selector
Amount Input
Account Selector
Category Selector
Date Input
Merchant Input
Description Input
Tag Display / Selection
Transaction Filters
```

## 22.4 Data Presentation

``` text
Financial Value
Percentage / Progress
Category Label
Account Type
Transaction Type
Transaction Source
Date
```

## 22.5 Feedback and Status

The UI must support the financial statuses already defined by the
application, including:

``` text
Budget usage
Goal progress
Recurring payment timing
Transaction type
```

No additional business status model is introduced in this specification.

------------------------------------------------------------------------

# 23. Responsive Design Strategy

## 23.1 Desktop

``` text
SIDEBAR + MAIN CONTENT
```

Desktop should prioritize:

-   Financial overview
-   Multi-column dashboard cards
-   Transaction tables/lists
-   Report visualizations
-   Persistent navigation

## 23.2 Tablet

``` text
COLLAPSIBLE SIDEBAR + MAIN CONTENT
```

Tablet should preserve the desktop information hierarchy while reducing
simultaneous content density.

## 23.3 Mobile

Mobile should prioritize:

-   Dashboard
-   Transactions
-   Add Transaction
-   Budget visibility
-   Fast navigation

Example:

``` text
┌──────────────────────────┐
│ Dashboard            ◉   │
│                          │
│ Total Balance            │
│ ₱156,450                 │
│                          │
│ ┌──────────┐ ┌─────────┐ │
│ │ Income   │ │Expense  │ │
│ │ ₱95,000  │ │₱52,340  │ │
│ └──────────┘ └─────────┘ │
│                          │
│ Monthly Budget           │
│ █████████████░░ 75%      │
│                          │
│ Recent Transactions      │
│ Starbucks       -₱250    │
│ Grab            -₱340    │
│ Salary       +₱45,000    │
│                          │
├──────────────────────────┤
│ Home   Txns   +   Budget │
└──────────────────────────┘
```

The Add Transaction action should remain easy to reach because manual
transaction management is the first V1 product priority.

------------------------------------------------------------------------

# 24. Mobile Transaction Flow

``` text
HOME
  │
  ▼
ADD
  │
  ├── EXPENSE
  ├── INCOME
  └── TRANSFER
        │
        ▼
TRANSACTION FORM
        │
        ▼
SAVE
        │
        ▼
TRANSACTION DATA
        │
        ▼
UPDATED FINANCIAL VIEW
```

The mobile flow should minimize navigation between choosing a
transaction type and entering the transaction information.

------------------------------------------------------------------------

# 25. Dashboard Responsive Priority

When screen space becomes smaller, dashboard information should remain
ordered by importance.

``` text
1. Total Balance
2. Income / Expenses
3. Monthly Budget
4. Recent Transactions
5. Spending by Category
6. Upcoming Bills
7. Savings Progress
```

This is a presentation priority for the dashboard information already
defined in the architecture and does not add new business capabilities.

------------------------------------------------------------------------

# 26. Data Visualization Principles

Recharts is the approved chart technology.

Charts should be used for information already defined in the product:

-   Spending by Category
-   Income vs Expenses
-   Monthly Spending Trend
-   Budget vs Actual
-   Cash Flow
-   Savings Rate

Charts should supplement financial values rather than replace them.

Example:

``` text
Spending by Category

        DONUT CHART

Food             ₱12,300  24%
Housing          ₱15,000  29%
Transportation    ₱6,400  12%
```

The user should still be able to understand the important financial
value without interpreting the visualization alone.

------------------------------------------------------------------------

# 27. Financial Number Presentation

Amounts should be consistently formatted and visually aligned with their
meaning.

Examples:

``` text
₱156,450
Total Balance

+ ₱45,000
Income

- ₱250
Expense
```

The application architecture supports multi-currency data through:

``` text
amount
currency
exchange_rate
base_amount
base_currency
```

The interface should therefore avoid designing monetary components that
assume all future values are permanently tied to one currency.

------------------------------------------------------------------------

# 28. Transaction Search and Filtering Flow

The existing transaction API supports:

``` text
from
to
category
account
type
page
limit
```

The interface should expose the relevant user-facing filters:

``` text
Search transactions...

[All Accounts ▼]
[All Categories ▼]
[Type ▼]
[Date ▼]
```

Flow:

``` text
TRANSACTIONS
     │
     ▼
SELECT FILTER
     │
     ▼
FILTERED TRANSACTION LIST
```

------------------------------------------------------------------------

# 29. Dashboard-to-Detail Navigation

The dashboard should function as an overview and entry point into the
financial domains.

``` text
DASHBOARD
   │
   ├── Balance / Accounts
   │         ↓
   │      ACCOUNTS
   │
   ├── Recent Transactions
   │         ↓
   │    TRANSACTIONS
   │
   ├── Monthly Budget
   │         ↓
   │      BUDGETS
   │
   ├── Upcoming Bills
   │         ↓
   │     RECURRING
   │
   ├── Savings Progress
   │         ↓
   │       GOALS
   │
   └── Spending Analytics
             ↓
          REPORTS
```

This keeps the dashboard useful without duplicating complete domain
screens.

------------------------------------------------------------------------

# 30. Phase 1 Screen Map

``` text
AUTH
├── Login
└── Register

APPLICATION
├── Dashboard
│
├── Accounts
│
├── Transactions
│   ├── Transaction List
│   └── Transaction Form
│       ├── Expense
│       ├── Income
│       └── Transfer
│
├── Budgets
│
├── Goals
│
├── Recurring Expenses
│
├── Reports
│
└── Settings
```

Categories and tags are supporting transaction concepts and should
appear where required by the transaction experience.

------------------------------------------------------------------------

# 31. Phase 2 UI Evolution

Phase 2 introduces only the capabilities already approved:

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

The existing navigation and dashboard should be capable of evolving to
present these capabilities without redesigning the entire application
shell.

## 31.1 Financial Health

Defined example:

``` text
FINANCIAL HEALTH SCORE

78 / 100
GOOD
```

Possible factors already established:

``` text
Savings rate
Budget discipline
Debt ratio
Emergency fund coverage
Recurring cost ratio
Income-to-expense ratio
```

## 31.2 Forecasting

Defined presentation model:

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

These are Phase 2 capabilities and should not be presented as available
MVP functionality.

------------------------------------------------------------------------

# 32. Phase 3 UI Evolution

Phase 3 introduces:

``` text
Bank synchronization
Automatic transaction import
Automatic categorization
Recurring-payment detection
Duplicate detection
Merchant normalization
```

The existing transaction UI must therefore be capable of displaying
transactions whose source is:

``` text
BANK_SYNC
```

The Phase 1 transaction design already accounts for transaction sources,
allowing the interface to evolve without changing the core transaction
concept.

------------------------------------------------------------------------

# 33. Phase 4 UI Evolution

Phase 4 introduces:

``` text
AI insights
Natural-language queries
Spending predictions
Expense anomaly detection
Budget recommendations
Savings recommendations
```

Examples already established include:

``` text
Your food spending increased 31%
compared with last month.
```

and:

``` text
This ₱18,500 transaction is significantly
higher than your normal shopping expenses.
```

Natural-language example:

``` text
How much did I spend eating outside during the last three months?
```

These intelligent capabilities should build on the existing dashboard,
reports, transaction, budget, and goal experiences rather than replacing
them.

------------------------------------------------------------------------

# 34. UX Scope Control

This UI/UX specification intentionally does not introduce product
capabilities outside the approved architecture.

It does not define:

-   New financial domains
-   New MVP modules
-   New account types
-   New transaction types
-   New transaction sources
-   New reports beyond the approved reports
-   New budget thresholds
-   New recurring frequencies
-   New Phase 2, Phase 3, or Phase 4 features

Where the source architecture does not define detailed behavior, the UI
specification keeps the design at the structural level rather than
creating unsupported business rules.

------------------------------------------------------------------------

# 35. Final UI/UX Direction

The application should feel like a **Personal Financial Command
Center**, not a traditional bookkeeping interface.

The experience should emphasize:

``` text
Clear financial overview
        ↓
Fast transaction entry
        ↓
Understandable account organization
        ↓
Visible budget progress
        ↓
Recurring financial awareness
        ↓
Goal progress
        ↓
Useful financial reporting
```

The strongest V1 experience should be:

``` text
LOGIN
  ↓
DASHBOARD
  ↓
ADD / REVIEW TRANSACTIONS
  ↓
UNDERSTAND SPENDING
  ↓
CONTROL BUDGET
  ↓
MONITOR RECURRING EXPENSES
  ↓
TRACK SAVINGS GOALS
  ↓
REVIEW REPORTS
```

The UI should preserve the established product journey:

> **Track → Understand → Budget → Plan → Predict → Improve**
