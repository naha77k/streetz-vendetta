# STREETZ VENDETTA SHOP

Netlify-ready community marketplace for the Roblox experience STREETZ VENDETTA.

## Included

- Responsive dark / red storefront using the supplied banner image.
- Seed catalogue with the 13 supplied Roblox Gamepasses and exact purchase URLs.
- Search, category filters, sorting, trending and latest sections.
- Guest browsing and Roblox-hosted purchase links (no fake checkout).
- Email/password accounts with server-side scrypt password hashing and HttpOnly sessions.
- Server-side role checks for Creator, Website Access, Payment Manager and Citizen.
- Product and announcement administration, Creator member management, bans and audit log.
- Persistent data using Netlify Blobs.

## Deploy to Netlify

1. Create a new Git repository and put the contents of this ZIP at its root.
2. In Netlify, choose **Add new site → Import an existing project** and connect the repository.
3. No build command is required. Publish directory is `public`; functions directory is `netlify/functions` (already configured in `netlify.toml`).
4. In **Site configuration → Environment variables**, add:
   - `CREATOR_SETUP_SECRET`: a long, random one-time setup secret (at least 32 random characters).
5. Deploy the site. Netlify Blobs is available to deployed Netlify Functions; no external database credentials are required for this starter.
6. Provision the Creator account exactly once. Replace `YOUR_SITE_DOMAIN` and `YOUR_SETUP_SECRET` and run this request from a trusted terminal (do not put the secret in frontend code):

```bash
curl -X POST "https://YOUR_SITE_DOMAIN/api/setup-creator" \
  -H "Content-Type: application/json" \
  -H "x-creator-setup-secret: YOUR_SETUP_SECRET" \
  -d '{"email":"YOUR_CREATOR_EMAIL","password":"A-unique-password-at-least-14-chars"}'
```

7. Sign in on the site with username `Naha_Dev1` and the password you supplied. After successful provisioning, the setup endpoint permanently refuses a second setup. Remove `CREATOR_SETUP_SECRET` from Netlify after setup.

## Local development

```bash
npm install
npx netlify dev
```

Netlify Blobs local development may require Netlify CLI linked to a site (`netlify link`) and local environment configuration. Use a deployed Netlify preview for the most reliable persistence test.

## Important implementation notes

- Roblox purchase links are validated server-side to ensure they point to `www.roblox.com/game-pass/...`.
- Roblox is the payment destination. This site does not collect payment details or claim to apply an automatic 25% discount.
- The Creator username is reserved case-insensitively. The Creator account is provisioned through the one-time secret endpoint, never with a hardcoded password.
- Netlify Blobs is persistent storage, but it is not a relational database with multi-record transactions. For a very high-traffic production launch requiring strict transactional guarantees across simultaneous registrations and complex member operations, migrate the account index to a transactional database (for example, Postgres) and retain Netlify Functions as the API layer.
- The supplied catalogue is initialized from the requested list. The Cash category exists and is empty until products are added.
- Product images can be supplied as HTTPS image URLs or uploaded as PNG/JPEG/WebP files (maximum 1.2 MB per image). Images are stored with the product record in Netlify Blobs.
- This project is a community storefront and is not an official Roblox-operated website.

## Main files

- `public/index.html` — page structure
- `public/styles.css` — responsive visual design
- `public/app.js` — storefront and admin UI
- `netlify/functions/api.mjs` — server-side API and permissions
- `netlify.toml` — Netlify publish/functions/routing configuration
