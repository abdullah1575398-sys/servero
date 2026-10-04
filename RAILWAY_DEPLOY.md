# Railway Deployment Guide for Spacebar Server

## Quick Start

1. **Create a Railway Project**
   - Go to [Railway.app](https://railway.app)
   - Click "Create New Project"
   - Select "Deploy from GitHub repo"
   - Choose this repository

2. **Add PostgreSQL Database**
   - In Railway dashboard, click "Add Service"
   - Select PostgreSQL
   - Railway auto-injects `$DATABASE_URL` into your app

3. **Configure Environment Variables**
   Copy the values from `.env.example` into Railway's Variables panel:

   **Essential (Required):**
   ```
   NODE_ENV=production
   PORT=3001
   DATABASE_URL=<auto-injected by Railway PostgreSQL>
   ```

   **Recommended (Set these):**
   ```
   API_ENDPOINT_PUBLIC=https://your-custom-domain.com
   API_ENDPOINT_PRIVATE=http://localhost:3001
   INSTANCE_NAME=My Spacebar Instance
   JWT_SECRET=<generate with: openssl rand -hex 32>
   ```

   **Optional (Email, leave empty if not needed):**
   - `SENDGRID_API_KEY` - for SendGrid
   - `MAILGUN_API_KEY` + `MAILGUN_DOMAIN` - for Mailgun
   - `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD` - for custom SMTP

4. **Deploy**
   - Railway auto-deploys on push to master
   - Check Deployment logs for errors
   - Visit the health check: `https://<railway-url>/-/healthz`

## Health Check

Once deployed, verify the API is running:
```bash
curl https://<your-railway-url>/-/healthz
```

Should return `200 OK`.

## Database Initialization

The first deployment will:
1. ✅ Build TypeScript → JavaScript
2. ✅ Run migrations automatically
3. ✅ Initialize the database schema

**No manual setup required.**

## Troubleshooting

### App crashes on startup
- Check logs: Railway Dashboard → Logs tab
- Common issues:
  - Missing `DATABASE_URL` → Add PostgreSQL service
  - Wrong `PORT` → Keep as `3001` (Railway assigns external port)
  - Database connection timeout → PostgreSQL service may not be ready yet

### Health check fails
- App may still be booting (database migrations take ~30s first time)
- Check logs for errors
- Ensure `NODE_ENV=production`

### Slow first deployment
- First run includes TypeScript compilation + database setup
- Subsequent deployments are faster (cached)

## Custom Domain

1. In Railway, go to Settings → Domains
2. Add your custom domain
3. Update environment variables:
   ```
   API_ENDPOINT_PUBLIC=https://your-domain.com
   API_ENDPOINT_PRIVATE=http://localhost:3001
   ```
4. Redeploy (push to master or manually trigger)

## Production Checklist

- [ ] PostgreSQL service added
- [ ] `DATABASE_URL` set (auto-injected by Railway)
- [ ] `JWT_SECRET` generated and set
- [ ] `API_ENDPOINT_PUBLIC` set to your domain
- [ ] `INSTANCE_NAME` customized
- [ ] Health check passes (`/-/healthz`)
- [ ] Email service configured (if needed)
- [ ] Logs checked for warnings

## Scaling

Railway automatically handles:
- ✅ Load balancing
- ✅ Container restarts on failure
- ✅ Auto-scaling (with paid plan)

For clustering across multiple workers, set `THREADS` environment variable (default: 1).

## Logs & Monitoring

- View logs: Railway Dashboard → Deployment → Logs
- Export logs: Click download button
- Set up alerts: Settings → Notifications

---

**Need help?** Check [Spacebar Docs](https://docs.spacebar.chat) or [Railway Docs](https://docs.railway.app)
