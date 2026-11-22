# ☕ Chai Sutta — Café Management System

A full-stack café operations application with a dark-green, warm-cream dashboard inspired by the supplied screen recording. The video is a visual reference; the café management modules are custom additions.

## Features

- **Dashboard:** revenue and order KPIs, daily sales chart, best-selling products, low-stock alerts.
- **POS:** searchable categorized menu, cart, quantity controls, customer name, cash/UPI/card payment recording, transactional checkout.
- **Order history:** orders, items, totals, timestamps and payment methods.
- **Menu management:** add products, pricing, categories and activate/deactivate products (admin).
- **Inventory:** current stock, low-stock indicators, manual adjustments and stock movement ledger (admin).
- **Reports:** revenue trends, average order value, best sellers and payment mix.
- **Authentication:** password-hashed admin login, signed JWTs, guarded API routes, admin-only mutations.
- **API documentation:** Swagger at `/docs`.

## Tech stack

React 18, TypeScript, Vite, Recharts, Lucide icons, NestJS 10, TypeORM, PostgreSQL 16, Docker Compose, GitHub Actions.

## Requirements

- Node.js 20+ (Node.js 22 recommended)
- npm 10+
- Docker and Docker Compose

## Quick start

```bash
git clone <YOUR_REPOSITORY_URL>
cd chai-sutta-management
cp .env.example .env
cp apps/web/.env.example apps/web/.env
# Set JWT_SECRET to a strong random value in .env
npm install
docker compose up -d
set -a; source .env; set +a
npx ts-node --project apps/api/tsconfig.json apps/api/src/seed.ts
npm run dev
```

Open **http://localhost:5173**. API: **http://localhost:3000/api**. Swagger: **http://localhost:3000/docs**.

Default local demo credentials (change via `.env` **before seeding**):

- Email: `admin@chaisutta.local`
- Password: `ChangeMe123!`

The demo credentials are intended **only for local development**. The seed script creates an admin if one does not already exist; changing the environment password later does not reset an existing account.

> The backend reads environment variables from the process. `source .env` (or your preferred environment loader) before starting. In another terminal, source `.env` again if running commands separately.

## Scripts

```bash
npm run dev                 # API + web in watch mode
npm run build               # Compile both apps
npm run dev -w apps/api     # API only
npm run dev -w apps/web     # Web only
```

## API routes

| Method | Path | Description | Role |
|---|---|---|---|
| POST | /api/auth/login | Login | Public |
| GET | /api/auth/me | Current JWT claims | Authenticated |
| GET | /api/products | List products | Authenticated |
| POST | /api/products | Create product | Admin |
| PATCH | /api/products/:id | Update product | Admin |
| DELETE | /api/products/:id | Deactivate product | Admin |
| POST | /api/products/:id/stock | Adjust stock | Admin |
| GET | /api/orders | Recent 100 orders | Authenticated |
| POST | /api/orders | Place order and decrement stock | Authenticated |
| GET | /api/reports/summary | Sales summary | Authenticated |

### Example order

```json
{
  "items": [{"productId": "<product-uuid>", "quantity": 2}],
  "paymentMethod": "UPI",
  "customerName": "Walk-in"
}
```

Amounts are integer **INR**, no floating-point rounding. Checkout calculates totals server-side from database prices. It uses PostgreSQL transactions and row locks to prevent overselling. Stock movements record order deductions and manual changes.

## Architecture

```text
apps/web (React SPA)
    | HTTP + JWT
    v
apps/api (NestJS controllers / TypeORM)
    | transactions + row locking
    v
PostgreSQL (users, products, orders, order_items, stock_movements)
```

## Repository structure

```text
apps/api/src/       NestJS modules, entities, seed
apps/web/src/       React dashboard and styling
.github/workflows/  Build CI
```

## Production readiness / limitations

This is a **working starter/MVP**, not a certified production POS. Before real café deployment:

1. Replace TypeORM `synchronize: true` with reviewed migrations and set `DB_SYNC=false`.
2. Use HTTPS, a managed secret store, short-lived tokens + refresh-token rotation, and account lockout/rate limiting.
3. Add staff creation, staff password reset, role management, and comprehensive audit logging.
4. Add taxes, discounts, refunds, cancellation and reversal workflows, receipt printing, and shift closeout.
5. Integrate a payment gateway to **verify** UPI/card transactions; currently payment method is only **recorded**, not processed.
6. Add pagination, product edits in UI, ingredient-level inventory, low-stock notifications, multi-outlet support, and exports.
7. Add integration/e2e tests, backups, monitoring, and deployment manifests.
8. Review CORS and authorization per environment; never use demo credentials in production.

## GitHub publishing

```bash
git init
git add .
git commit -m "feat: initial chai sutta cafe management MVP"
git branch -M main
gh repo create chai-sutta-management --public --source=. --remote=origin --push
```

Requires GitHub CLI (`gh`) installed and authenticated. Alternatively create a repository in GitHub and use `git remote add origin <URL>; git push -u origin main`.

## License

MIT. This is an independent portfolio demo, not an official Chai Sutta brand product.
