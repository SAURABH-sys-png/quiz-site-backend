# Quiz Platform Backend

This repository contains the Express + MongoDB API. Deploy it as a Koyeb Web Service separately from the
frontend Vercel project. Koyeb runs [`src/server.js`](./src/server.js), including Socket.IO and the scheduled
test/attempt sweeper.

## Koyeb

Create an app from the `quiz-site-backend` GitHub repository using Koyeb's **Deploy from Git** flow. Select the
repository root, choose the Node.js buildpack, set the build command to `npm ci` and the run command to
`npm start`. Configure an HTTP service port of `5000`, set `PORT=5000`, and add `/api/health` as the health-check
path. Deploy one instance and generate a public Koyeb domain.

Set these environment variables in the Koyeb service:

- `MONGODB_URI`: MongoDB Atlas connection string. Configure Atlas network access to allow Koyeb.
- `GOOGLE_CLIENT_ID`: Google OAuth Web client ID.
- `ADMIN_EMAILS`: comma-separated Google account emails permitted to administer the app.
- `CLIENT_ORIGIN`: exact frontend Vercel origin, without a trailing slash.
- `NODE_ENV`: `production`
- `JWT_SECRET`: random secret of at least 32 characters
- `COOKIE_SECURE`: `true`
- `COOKIE_SAME_SITE`: `none` for separate Vercel/Koyeb domains
- `TRUST_PROXY`: `1`
- `CODE_EXECUTION_ENABLED`: `false`

Koyeb requires a payment card for account verification and may place a temporary pre-authorization hold, even
when selecting its free instance. Check Koyeb's [pricing FAQ](https://www.koyeb.com/docs/faqs/pricing) before
signing up if you do not want to provide payment details.

Add the frontend's Vercel domain to the Google OAuth client's **Authorized JavaScript origins**. Koyeb's Edge
Network can close WebSocket connections after 12 hours; Socket.IO will reconnect, but uninterrupted connections
are not guaranteed. See [Koyeb's Edge Network documentation](https://www.koyeb.com/docs/reference/edge-network).

Browser third-party-cookie restrictions may block authentication across unrelated `*.vercel.app` and
Koyeb domains even with `SameSite=None`. For reliable cookies, use custom frontend/API domains under the same
site (for example, `quiz.example.com` and `api.example.com`) and set `COOKIE_SAME_SITE=lax` in Koyeb. Update
`CLIENT_ORIGIN` to the exact frontend origin if its domain changes.

## Local development

```bash
npm install
npm run dev
npm test
```

Copy `.env.example` to `.env` and configure MongoDB, JWT, and Google OAuth values for local development.
On Koyeb, set environment variables through the service settings; never commit `.env`.

## Question import

The [`question-papers/`](./question-papers/) folder contains the supplied C++ constructors/destructors/
encapsulation paper in the admin bulk-import JSON format. Upload the JSON in a test's **Bulk import** panel,
review the preview, and confirm the import.
