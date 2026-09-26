# Factory Order Management ERP — PRD

## Original Problem Statement
Full-stack Factory Order Management ERP (React + FastAPI + MongoDB): user roles, order tracking, dispatch management, customer ledgers, reporting. The user iterates by providing updated GitHub repos and asking for FULL codebase replacements (current source of truth: https://github.com/noork592/39.git).

## Architecture
- Frontend: React (`/app/frontend`), Tailwind, shadcn/ui, react-router-dom, craco, PWA (service worker), i18n (en/hi)
- Backend: FastAPI (`/app/backend/server.py`, ~8400 lines), Motor/MongoDB, JWT auth, bcrypt
- Auth: `POST /api/auth/login` (email OR username in `email` field), `POST /api/auth/verify-otp`, OTP optional per-user (`otp_login`, default OFF in repo 39)
- Health: `GET /health` (root, used by platform probe). NOTE: `/api/health` is defined AFTER `app.include_router(api_router)` in repo 39's server.py so it 404s — repo bug, harmless.
- Env: `/app/backend/.env` (MONGO_URL, DB_NAME, JWT_*, EMERGENT_LLM_KEY, GMAIL_*) and `/app/frontend/.env` (REACT_APP_BACKEND_URL) are LOCAL ONLY — repo has no .env files (gitignored). Never delete them during rsync --delete (exclude '.env').

## Repo Sync Procedure (user's recurring request)
1. `git clone --depth 1 <repo> /tmp/repoN`
2. `rsync -av --delete --exclude='.env' --exclude='__pycache__' /tmp/repoN/backend/ /app/backend/`
3. `rsync -av --delete --exclude='.env' --exclude='node_modules' /tmp/repoN/frontend/ /app/frontend/`
4. Copy root extras (README, backend_test.py, tests/, test_reports/) — never touch /app/.git, /app/.emergent
5. `yarn install` (frontend), `pip install -r requirements.txt` if changed
6. `sudo supervisorctl restart all`, smoke check `/health` + login page

## Implemented (history)
- Sequential repo syncs: 6-AUG → 24aug → 25aug → 28f → 29f → 30 → 32 → 34 → 36 → 38 → **39 (current, 2026-09-26)**
- Repo 39 highlights: Facebook-style login page (by design), Estimates, PurchaseCenter, Suppliers/Vendor ledgers, TransportRoutes, AI chatbot, price lists, login attestation flow
- Past custom work (may or may not survive repo replacements): OTP toggle, JK1 blank-view user, order/dispatch edit fixes, pvt-marka/bill-number exclusivity, leaflet maps (repo 38)

## Known Issues / Pending
- Admin DB password no longer matches `admin123` (changed previously in-app; DB untouched by syncs). User may know current password; offer reset if they can't log in.
- "Customer not found" when typing bill number in Daily Report pvt-marka field (reported pre-repo-38; recheck in repo 39 if user reports again)
- `/api/health` 404 (route ordering bug in user's repo; root `/health` fine)

## Next Action Items
- Confirm user can log in with their current password; reset to admin123 if requested
- Test repo 39 flows when user requests (user declined testing for this sync)
