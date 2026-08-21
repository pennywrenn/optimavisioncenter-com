# optimavisioncenter.com

Static site for Optima Vision Center — Dr. Chelise Firmin, OD · 6331 Stenton Ave,
Philadelphia, PA 19138 · (215) 548-5949

One page, no build step, no dependencies. `index.html` plus `optima_photo_assets/`.
Edit the HTML directly and push; GitHub Pages redeploys in about a minute.

```
index.html               the site
optima_photo_assets/     images (15 files, 2.5 MB total)
CNAME                    optimavisioncenter.com — tells Pages which domain to serve
.nojekyll                stops Jekyll from touching the asset folder
```

---

## Publishing checklist

### 1. GitHub

- Push this folder to a repo.
- Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
- The `CNAME` file already sets the custom domain. If Settings → Pages shows the
  domain box empty, type `optimavisioncenter.com` and Save.
- Wait for the first deploy to go green before touching DNS.

### 2. GoDaddy DNS

**Leave the nameservers alone.** They stay at `ns05/ns06.domaincontrol.com`.
Only the records inside the zone change. This matters: the practice's Google
Workspace MX records live in this same zone, and changing nameservers is how people
lose their email.

**Do not touch the MX records.** There are five, all `aspmx.l.google.com` variants.

Delete:

| Type | Name | Value |
|---|---|---|
| A | `@` | `103.169.142.0` (the current Canva site) |

Add — all four, same name, they are a set:

| Type | Name | Value | TTL |
|---|---|---|---|
| A | `@` | `185.199.108.153` | 600 |
| A | `@` | `185.199.109.153` | 600 |
| A | `@` | `185.199.110.153` | 600 |
| A | `@` | `185.199.111.153` | 600 |
| CNAME | `www` | `<username>.github.io` | 600 |

Optional IPv6, same idea:
`2606:50c0:8000::153`, `8001::153`, `8002::153`, `8003::153` as AAAA on `@`.

### 3. HTTPS

GitHub requests a Let's Encrypt certificate **after** the domain resolves to its
IPs. Between DNS propagating and the certificate issuing, the site serves a
certificate for the wrong hostname and the browser throws
"Your connection is not private". **This is expected and it clears on its own** —
usually well under an hour, occasionally up to 24.

Do not change DNS again while waiting. That restarts the clock and is the single
most common way this gets stuck.

Once it clears, tick **Enforce HTTPS** in Settings → Pages.

If `Enforce HTTPS` is still greyed out after 24 hours: remove the custom domain in
Settings → Pages, Save, re-add it, Save. That forces re-provisioning.

### 4. Verify

Checked as of Aug 21, 2026, before any changes:

- Nameservers: GoDaddy ✅ (leave as is)
- Apex A record: `103.169.142.0` — Canva, must be replaced
- MX: Google Workspace, five records, intact — must stay intact
- CAA: **none set** ✅ — a CAA record that omits `letsencrypt.org` silently blocks
  certificate issuance. There isn't one, so that failure mode is off the table.
- `optimavisioncenter.net`: NXDOMAIN (expired). The old site's "Schedule
  Appointment" and "Patient Forms" links pointed here, which is why they were
  already broken before the outage.

---

## Known open items

See `BUILD_NOTES.md` in the staging folder. Short version: **hours are unconfirmed**
(from an archived capture; two captures disagreed), insurance acceptance is
unlisted, and the Google and Yelp links have not been click-verified.
