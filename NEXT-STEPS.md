# Handoff — where things stand

Status as of **28 September 2026**. The site is live at https://www.embolizationinc.com, deploys
automatically from this repo, and the bare domain redirects to it. What remains is content review and
a couple of checks.

---

## 1. How the site is hosted

| | |
|---|---|
| Host | **Cloudflare Workers** — project `embolization-inc-site` |
| Deploys | Automatically on every push to `main`. Usually live within a minute; allow up to ~10. The build status shows as a check on each commit in GitHub. |
| Headers | `_headers` at the repo root (asset caching, security headers). `vercel.json` is unused — left over from an earlier hosting option. |
| Staging copy | GitHub Pages also builds from `main`: https://boulderbiomed.github.io/embolization-inc-site/ |

**To make an edit:** change the files, commit, push to `main`, then check the live site.

**Caching:** files in `assets/` are cached by browsers for a year. After editing `styles.css` or
`main.js`, bump the `?v=` number on its link in `index.html`, or returning visitors keep the old file.

---

## 2. Domain and DNS

| | |
|---|---|
| Registrar | Squarespace Domains |
| DNS | **Cloudflare** (zone `embolizationinc.com`) |
| `www` | Worker record → `embolization-inc-site` (proxied). This is the live site. |
| `embolizationinc.com` (bare) | A record `192.0.2.1`, **proxied** — a placeholder only. A Redirect Rule named **"Root to www"** sends every request to `https://www.embolizationinc.com/…` (301, path and query string kept). |
| Mail | Microsoft 365 — MX, `v=spf1` TXT and `autodiscover` CNAME |

⚠️ **Never edit or delete the MX, `v=spf1` TXT or `autodiscover` records.** Changing them breaks
company email.

The bare-domain redirect only works while its A record stays **proxied** (orange cloud). If it is set
to DNS only, the redirect rule stops applying.

### History and rollback

The previous site was built on Manus. Until 28 September 2026 the bare domain still pointed at it via
two DNS-only A records, `104.18.27.246` and `104.18.26.246`. Nothing points at Manus any more, so
that site can be retired. Restoring those two records (DNS only) would bring it back on the bare
domain, if ever needed.

---

## 3. Review the content

The copy came across from the previous site. The clinical figures and the artifact measurement table
are new — they're the real measured data from the CT Imaging Comparison Report (TR 005017 / VP 004731),
and each caption states its actual artifact width and scan configuration.

Worth a careful read:

- **The measurement table** in the "Metal Coils Blind Your Follow-Up Imaging" section. Check the
  numbers against the source report.
- **Every figure caption.** They make specific claims (20%, 57%, 58%, 64% less artifact). They should
  match the figure directly above them.
- **The safety section** at the bottom — indications, contraindications, warnings, precautions,
  adverse events. Confirm it still matches the current IFU.
- **The team section.** Five people listed. Confirm titles and bios are current.

### Known content gaps

| Gap | Detail |
|---|---|
| No product photograph | There is no photo of the NED coil anywhere on the site. The previous site had one; the file was lost. Worth shooting or sourcing. |
| No deployment video | Same story — the previous site had a deployment animation. |
| GLP sheep section has no image | **Intentional.** All available imagery is bench phantom or human in-vivo CT; none is from the sheep study that section describes. It presents the data table alone. Only genuine sheep study imagery belongs there — see the rule in `README.md`. |

---

## 4. Confirm the contact form is delivering

The form posts to FormSubmit, which was activated on 1 September 2026, and should deliver to
`inquire@embolizationinc.com`.

**Still to do:** submit the form on https://www.embolizationinc.com with your own email in the message,
and confirm it arrives at `inquire@embolizationinc.com`. It was only tested on the staging URL before
the site went live.

The endpoint is one constant at the top of `assets/js/main.js`:

```js
var FORM_ENDPOINT = 'https://formsubmit.co/ajax/inquire@embolizationinc.com';
```

Swap that line to move to any other provider. If the endpoint ever fails, the form falls back to a
pre-filled `mailto:` link rather than silently dropping the submission.

---

## 5. Worth raising with Jim

This repo lives under the **`BoulderBioMed`** account, alongside the other Boulder sites — good.
One structural thing is still open.

`BoulderBioMed` is a GitHub **user account**, not an organization. That means a single shared login
and password rather than individual accounts, no per-person roles, and no way to remove one person's
access without changing it for everyone. It also means collaborators on its repos can only be granted
write — GitHub has no admin role on user-owned repositories.

Converting it to a real organization is free and keeps every repo and the name. Afterwards each person
signs in as themselves as a member of the org, access is granted and revoked per person, and there is
no shared password to circulate.

That matters here specifically: this rebuild was necessary because the previous site existed inside
one vendor account that nobody could recover. A shared login is the same shape of risk.
