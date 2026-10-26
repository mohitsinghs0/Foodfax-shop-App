# FoodFax Flutter Owner App — Security & Readiness Audit
**Audit date:** 2026-10-26  
**Scope:** Default-branch source/configuration inspection only. No Flutter analyze/test/build, production request, or database query was run.

## Findings
- **Configuration drift / legacy backend:** `.env.example`, `lib/core/constants.dart`, and README configuration point to the older Supabase project reference `aftmqdmiwvpbpsdmsfbu`. Confirm whether this repository is still an active deployment target before changing it. Do not migrate silently.
- **Credential configuration:** The committed key is labelled publishable/anon rather than service-role, so it is not equivalent to a privileged secret. Still, the production endpoint and publishable key should be supplied through build-time configuration and governed by strict database RLS; a publishable key is not an authorization mechanism.
- **Architecture overlap:** This repository duplicates owner functionality available in `FoodFax-shop` (React + Capacitor). Choose the authoritative owner app before behavior/security changes are applied to both.
- **Auth implementation:** `lib/core/supabase_client.dart` initializes Supabase with PKCE and auto-refresh. This source alone does not verify role authorization, cross-shop isolation, storage rules, or actual deployed database policies.
- **Data and logging:** Realtime logging is set to info; review production log verbosity and ensure personal data, tokens, KYC metadata, and payment details are not logged.

## Required verification before release
1. Confirm the intended backend and owner-app repository; compare schema and auth models before migration.
2. Test owner A cannot read or mutate owner B's shops, orders, menu, reports, or files.
3. Verify RLS and storage policies against the actual target database.
4. Ensure admin-only operations cannot be invoked by the owner app.
5. Run `flutter analyze`, `flutter test`, debug/release builds and device regression tests.
6. Check build artifacts and Git history for secrets or personal/KYC data; rotate actual privileged credentials if found.
7. Align environment configuration and docs only after the target backend is confirmed.

## Status
- Source/configuration inspection: **started**
- Runtime/build/tests: **not run**
- Production configuration/database: **not verified**
- Production readiness: **not certified**
