# GrowBoss AI SaaS MVP

A deployable Flask prototype with email/password accounts, per-account cloud-side workspaces, customers, leads, estimates, marketing templates, OpenAI chat, Stripe Checkout subscriptions, and Stripe Billing Portal.

## Run locally

1. Install Python 3.11+ and run `pip install -r requirements.txt`.
2. Copy `.env.example` to your hosting environment and set `SECRET_KEY` (a long random value). For local HTTP testing, set `COOKIE_SECURE=0`.
3. Set `OPENAI_API_KEY` for AI chat, and Stripe environment variables for subscriptions. Do **not** put API keys in `index.html`.
4. Run `python app.py`, visit `http://localhost:8000`, and create an account.

## Deploy (e.g. Render)

1. Upload this folder to a private GitHub repository, create a **Web Service** on Render from that repo.
2. Build command: `pip install -r requirements.txt`; start command: `gunicorn --workers 2 --bind 0.0.0.0:$PORT app:app`.
3. Set environment variables from `.env.example`. Set `PUBLIC_BASE_URL` to your actual HTTPS deployment URL, `COOKIE_SECURE=1`.
4. **Use a persistent disk** mounted at `/data` and set `DATABASE_PATH=/data/growboss.sqlite3`. On ephemeral hosting SQLite data disappears on restart. A production multi-instance deployment should migrate to managed Postgres instead.
5. In Stripe create a recurring monthly product/price, set `STRIPE_PRICE_ID`, and configure webhook endpoint `https://YOUR-DOMAIN/api/stripe/webhook` with events `checkout.session.completed`, `customer.subscription.created`, `customer.subscription.updated`, and `customer.subscription.deleted`. Copy signing secret to `STRIPE_WEBHOOK_SECRET`. Enable customer portal in Stripe.
6. Test checkout using Stripe test keys and test cards before switching to live keys.

## Important launch checklist

This is an **MVP**, not an audited production service. Before accepting real customer data or payments: add email verification, password reset, rate limits and bot protections, robust CSRF protection, HTTPS-only deployment, monitoring, backups, legal pages (privacy policy and terms), data export/deletion workflows, and a managed database. Restrict registration until those are done. Avoid collecting sensitive customer data. Billing is not activated by the checkout redirect alone; verified Stripe webhooks update subscription status. Free AI quota defaults to 3/month; paid quota to 200/month. Chat history remains browser-session-local, while business records are stored server-side. The app uses a single workspace per account and is not yet multi-user/team-enabled.
