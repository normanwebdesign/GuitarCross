# Deploying Guitar Cross to Netlify

Netlify gives the app the one thing it cannot get from a file on your phone:
**https**. That is what lets iOS hand the microphone to the tuner. Everything
else already works offline.

There is nothing to build. `netlify.toml` tells Netlify to publish this folder
as-is, and sets the headers the app needs.

---

## Option A - drag and drop (two minutes, no git)

1. Go to <https://app.netlify.com/drop>.
2. Drag the whole **GuitarCross** folder onto the page.
3. Netlify gives you an address like `https://cheerful-pie-123abc.netlify.app`.
4. Open it in Safari on your phone → Share → **Add to Home Screen**.

Good for trying it out. To publish a change you drag the folder again, which
creates a new address unless you link the drop to an existing site.

## Option B - connect the git repo (what you want long term)

This folder is the clone of **github.com/normanwebdesign/GuitarCross** and the
remote is already set, so the whole history just needs pushing:

```bash
git push -u origin main
```

Then in Netlify: **Add new site → Import an existing project → GitHub**, pick
**GuitarCross**. Leave the build command **empty** and the publish directory as
**`.`** - `netlify.toml` already says this, so the defaults it offers should be
right. Every `git push` from then on redeploys the site.

## Option C - Netlify CLI (fastest here)

The CLI is already installed on this machine and you are already logged in as
`jordanbabb@normanwebdesign.com`. This folder just is not linked to a site yet.
Use this if you would rather not go through GitHub.

First time - create the site and publish it in one go:

```bash
netlify sites:create --name guitar-cross
```

```bash
netlify deploy --prod --dir .
```

`sites:create` asks which team to put it under (**Better Website** or
**norman web design's team**) and links this folder to the new site. After that,
`netlify deploy --prod --dir .` is all it takes to publish a change.

To link this folder to a site that already exists instead:

```bash
netlify link
```

---

## After the first deploy

**Rename the site** so the address is something you can type: Site configuration
→ Site details → Change site name, e.g. `guitar-cross` →
`https://guitar-cross.netlify.app`.

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
const CACHE = 'guitar-cross-v4';   // v3 -> v4
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
