# Cribside Website — Setup & Deployment

Step-by-step guide to deploy the Cribside website to GitHub Pages with the `cribside.app` custom domain and `support@cribside.app` email.

---

## 1. Push to GitHub

If the repo isn't already on GitHub:

```bash
cd /Users/matt/Documents/Sync/git/babyApp
git remote add origin git@github.com:YOUR_USERNAME/babyApp.git
git push -u origin main
```

## 2. Enable GitHub Pages

1. Go to **github.com → your repo → Settings → Pages**
2. Under **Source**, select: **Deploy from a branch**
3. Set **Branch:** `main`, **Folder:** `/website`
4. Click **Save**
5. GitHub will build and deploy in ~1 minute

Your site will initially be available at `https://YOUR_USERNAME.github.io/babyApp/`.

## 3. Configure Custom Domain (cribside.app)

### 3a. GitHub Pages side

1. Still in **Settings → Pages**, enter `cribside.app` in the **Custom domain** field
2. Click **Save**
3. Check **Enforce HTTPS** (required for .app domains anyway)

### 3b. Squarespace DNS side

Log in to **[domains.squarespace.com](https://domains.squarespace.com)** → select **cribside.app** → **DNS** → **DNS Settings**.

**Delete any existing A or CNAME records** for the root domain, then add these:

#### A Records (point root domain to GitHub Pages)

| Type | Host | Value |
|------|------|-------|
| A | @ | `185.199.108.153` |
| A | @ | `185.199.109.153` |
| A | @ | `185.199.110.153` |
| A | @ | `185.199.111.153` |

#### CNAME Record (www redirect)

| Type | Host | Value |
|------|------|-------|
| CNAME | www | `YOUR_USERNAME.github.io` |

Replace `YOUR_USERNAME` with your GitHub username.

### 3c. Wait for propagation

DNS changes typically take 5–30 minutes but can take up to 48 hours. You can check progress with:

```bash
dig cribside.app +short
# Should return the GitHub Pages IPs

dig www.cribside.app +short
# Should return YOUR_USERNAME.github.io
```

### 3d. Verify HTTPS

After DNS propagates, GitHub automatically provisions an SSL certificate via Let's Encrypt. This can take up to an hour. The `.app` TLD enforces HTTPS via the HSTS preload list, so this is mandatory.

Visit `https://cribside.app` — it should load your site with a valid certificate.

## 4. Set Up Email Forwarding (support@cribside.app)

Apple requires a support email. You need `support@cribside.app` to forward to your personal email.

### Option A: Squarespace Email Forwarding (simplest)

1. Log in to **[domains.squarespace.com](https://domains.squarespace.com)**
2. Select **cribside.app** → **Email** → **Email Forwarding**
3. Add a forwarding address:
   - **From:** `support@cribside.app`
   - **To:** your personal email address
4. Click **Save**

Squarespace will automatically add the necessary MX records.

### Option B: Cloudflare Email Routing (free, more control)

If you later move DNS to Cloudflare:

1. Add your domain to Cloudflare
2. Go to **Email → Email Routing**
3. Add a rule: `support@cribside.app` → your personal email
4. Cloudflare adds MX and TXT records automatically

### Add SPF Record (prevents forwarded emails from going to spam)

| Type | Host | Value |
|------|------|-------|
| TXT | @ | `v=spf1 include:squarespace.com ~all` |

If using Cloudflare instead, replace `squarespace.com` with `cloudflare.net`.

### Test it

Send a test email to `support@cribside.app` and verify it arrives in your personal inbox.

## 5. Set Up cribsides.com Redirect (optional)

If you own `cribsides.com`, set up a 301 redirect:

1. At your registrar for `cribsides.com`, find **Domain Forwarding** or **Redirect** settings
2. Forward to `https://cribside.app` with a **301 (permanent)** redirect
3. Enable **Path forwarding** if available (so `cribsides.com/privacy` → `cribside.app/privacy`)

## 6. Verify Everything

Run through this checklist:

- [ ] `https://cribside.app` loads the landing page
- [ ] `https://cribside.app/privacy/` loads the privacy policy
- [ ] `https://cribside.app/support/` loads the support page
- [ ] `https://cribside.app/terms/` loads the terms page
- [ ] `https://www.cribside.app` redirects to `https://cribside.app`
- [ ] HTTPS certificate is valid (padlock icon in browser)
- [ ] Email to `support@cribside.app` arrives in your inbox
- [ ] All nav links work on every page
- [ ] Site looks correct on mobile (test with phone or browser dev tools)

## 7. Enter URLs in App Store Connect

When you create the app record in App Store Connect, enter:

| Field | URL |
|-------|-----|
| Marketing URL | `https://cribside.app` |
| Privacy Policy URL | `https://cribside.app/privacy/` |
| Support URL | `https://cribside.app/support/` |

---

## Updating the Site

Edit the HTML files in the `website/` directory and push to `main`. GitHub Pages deploys automatically within ~1 minute.

### Switching the "Coming Soon" banner to "Live Now"

In `website/index.html`, find the announcement banner near the top:

```html
<!-- Change class to "announcement-banner live" and update text -->
<div class="announcement-banner">
  Coming Soon to the App Store
</div>
```

Change to:

```html
<div class="announcement-banner live">
  Now Available on the App Store
</div>
```

Also update the two "Coming Soon" buttons to link to your App Store listing:

```html
<a href="https://apps.apple.com/app/cribside/idXXXXXXXXXX" class="btn btn-primary">Download on the App Store</a>
```

## File Structure

```
website/
├── index.html              Landing page
├── privacy/index.html      Privacy policy (required by Apple)
├── support/index.html      Support + FAQ (required by Apple)
├── terms/index.html        Terms of use
├── css/style.css           Shared styles
├── images/app-icon.svg     Placeholder app icon
├── CNAME                   GitHub Pages custom domain
└── README.md               This file
```
