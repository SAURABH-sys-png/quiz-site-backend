# Quiz Platform Backend

This repository contains the Express + MongoDB API. Deploy it as a persistent Render Web Service, separately
from the frontend Vercel project. Render runs [`src/server.js`](./src/server.js), which keeps Socket.IO and the
scheduled test/attempt sweeper running.

## Render

Create a new Render Blueprint and select this repository. [`render.yaml`](./render.yaml) configures the service;
Render asks for the values marked `sync: false` when creating it. The Blueprint uses the paid Starter instance
so the service stays running for realtime connections and scheduled work.

Provide these values when prompted:

- `MONGODB_URI`: MongoDB Atlas connection string. Configure Atlas network access to allow the Render service.
- `GOOGLE_CLIENT_ID`: Google OAuth Web client ID.
- `ADMIN_EMAILS`: comma-separated Google account emails permitted to administer the app.
- `CLIENT_ORIGIN`: exact frontend Vercel origin, without a trailing slash.

The Blueprint generates a `JWT_SECRET`, sets `COOKIE_SECURE=true`, and configures `COOKIE_SAME_SITE=none` for
separate Render/Vercel sites. Add the frontend's Vercel domain to the Google OAuth client's **Authorized
JavaScript origins**.

Browser third-party-cookie restrictions may block authentication across unrelated `*.vercel.app` and
`*.onrender.com` domains even with `SameSite=None`. For reliable cookies, use custom frontend/API domains under
the same site (for example, `quiz.example.com` and `api.example.com`) and set `COOKIE_SAME_SITE=lax` in Render.
Update `CLIENT_ORIGIN` to the exact frontend origin if its domain changes.

## Local development

```bash
npm install
npm run dev
npm test
```

Copy `.env.example` to `.env` and configure MongoDB, JWT, and Google OAuth values for local development.
On Render, set environment variables through the Blueprint or service settings; never commit `.env`.

## Question import

The [`question-papers/`](./question-papers/) folder contains the supplied C++ constructors/destructors/
encapsulation paper in the admin bulk-import JSON format. Upload the JSON in a test's **Bulk import** panel,
review the preview, and confirm the import.
