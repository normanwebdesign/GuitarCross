# Deploying Cross Guitar to Netlify

Netlify gives the app the one thing it cannot get from a file on your phone:
**https**. That is what lets iOS hand the microphone to the tuner. Everything
else already works offline.

There is nothing to build. `netlify.toml` tells Netlify to publish this folder
as-is, and sets the headers the app needs.

---

## Option A - drag and drop (two minutes, no git)

1. Go to <https://app.netlify.com/drop>.
2. Drag the whole **Cross Guitar** folder onto the page.
3. Netlify gives you an address like `https://cheerful-pie-123abc.netlify.app`.
4. Open it in Safari on your phone → Share → **Add to Home Screen**.

Good for trying it out. To publish a change you drag the folder again, which
creates a new address unless you link the drop to an existing site.

## Option B - connect a git repo (what you want long term)

The repo is already initialised here with everything committed.

1. Create an empty repository on GitHub (no README, no .gitignore - this folder
   has both).
2. Point this folder at it and push:

```bash
git remote add origin https://github.com/<you>/cross-guitar.git
git push -u origin main
```

3. In Netlify: **Add new site → Import an existing project → GitHub**, pick the
   repository.
4. Leave the build command **empty** and the publish directory as **`.`** -
   `netlify.toml` already says this, so the defaults it shows should be right.
5. Deploy. Every `git push` from now on redeploys the site.

## Option C - Netlify CLI

```bash
npm install -g netlify-cli
netlify deploy --prod
```

Answer the prompts; publish directory is `.`.

---

## After the first deploy

**Rename the site** so the address is something you can type: Site configuration
→ Site details → Change site name, e.g. `cross-guitar` →
`https://cross-guitar.netlify.app`.

**Add it to your phone.** Safari → the address → Share → Add to Home Screen. It
launches full screen with no address bar and runs with no signal.

**Allow the microphone** the first time you tap Start on the Tune tab.

**A custom domain** (e.g. `guitar.normanwebdesign.com`) goes under Domain
management → Add a domain; Netlify issues the certificate itself.

---

## Shipping a change

```bash
git add -A
git commit -m "What changed"
git push
```

If you edited `index.html`, **bump the cache name in `sw.js`** first:

```js
const CACHE = 'cross-guitar-v2';   // v1 -> v2
```

That is what tells an installed phone to throw away the old copy. Without it,
a device that already has the app may keep serving the version it cached, even
though the new one is live.

## What the headers in netlify.toml do

| Header | Why |
| --- | --- |
| `Cache-Control: must-revalidate` on the page and `sw.js` | so a new deploy actually reaches installed phones |
| `Cache-Control: immutable` on the icons | they never change under the same name |
| `Content-Type` on `manifest.webmanifest` | some hosts serve it as plain text and the install prompt then ignores it |
| `Permissions-Policy: microphone=(self)` | keeps the tuner allowed to listen |
| `Content-Security-Policy` | the app loads nothing from other origins, so it is locked to its own; `data:`/`blob:` stay allowed for photos and backup exports |

## Your songs are not in the repo

They live in the browser on each device, not in these files. Deploying does not
move them, and clearing Safari data erases them. Use **Tools → Export backup**
before switching devices or clearing data; `.gitignore` keeps those backup files
out of the repo.
