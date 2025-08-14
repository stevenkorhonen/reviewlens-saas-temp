# ReviewLens SaaS (OpenAI-powered)

A production-ready multi-tenant SaaS that ingests customer reviews and returns summaries, themes, sentiment and actions. Frontend is your original React/Vite app, wired to this new Node/Express + Prisma backend. Billing hooks for Stripe are included.

## Quickstart (Local, with Docker)
1. Copy `.env.example` to `.env` and fill in `OPENAI_API_KEY` and a strong `JWT_SECRET`.
2. `docker compose up` (first run may take a bit).
3. In another terminal: `docker compose exec backend npx prisma migrate dev --name init`
4. Open the app at http://localhost:5173 and sign up, then analyze reviews.

## Manual Dev (without Docker)
- Postgres running locally, set `DATABASE_URL` in `.env`.
- Backend: `cd backend && npm i && npx prisma generate && npm run dev`
- Frontend: `cd frontend && npm i && npm run dev`

## API (selected)
- `POST /api/auth/signup` { email, password, name }
- `POST /api/auth/signin` { email, password }
- `GET /api/auth/me`
- `POST /api/analysis` { content }
- `GET /api/reviews` list, `POST /api/reviews` { source, content }

## Plans / Limits (example defaults)
- FREE: up to 2000 chars per analysis, 50 analyses/month
- PRO: up to 10,000 chars, 1000 analyses/month
- TEAM: Shared workspace, SSO, priority rate limits (extend in code)

## Notes
- Stripe is optional; set keys & price IDs to enable checkout + webhooks.
- For SEO + marketing site, consider moving the landing content into a Next.js site and proxy `/api` to backend.


## Billing (Stripe)
1. Create two recurring Prices in Stripe: `PRO` and `TEAM`. Copy their Price IDs to `.env` as `STRIPE_PRICE_PRO` and `STRIPE_PRICE_TEAM`.
2. Set `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, and `FRONTEND_URL` in `.env`.
3. Run: `docker compose up` and expose your webhook locally with `stripe listen --forward-to localhost:8787/webhooks/stripe`.
4. Trigger checkout from the app (PlanSelector), you’ll be redirected to Stripe Checkout.
5. On success, the webhook updates the user’s `plan` and `subscriptionStatus` to `active`.
