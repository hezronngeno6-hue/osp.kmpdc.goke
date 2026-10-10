# OSP Host — `osp.kmpdc.goke`

Static replication host for the **clinic / institution licence** record page.
This repo is the deploy target: pushing to `main` publishes the site.

- Repo: <https://github.com/hezronngeno6-hue/osp.kmpdc.goke>
- GitHub Pages: **not enabled.** Enabling it needs a repo-admin action in
  Settings -> Pages, because the workflow token cannot create a Pages site
  (`403 Resource not accessible by integration`), and the API reports 404.
- Live record therefore ships from the PPB Vercel host instead:
  `https://poisonsandpharmacyboard.vercel.app/clinics/clinic_007174_gashe`
  (this repo is the mirror / fallback if Pages is switched on later).

## Layout

```
vercel.json                             Vercel static config (outputDirectory: public)
package.json                            CLI metadata (npm run deploy)
public/index.html                       portal landing page
public/404.html                         fallback page
public/.nojekyll                        serve files as-is (left over from the Pages attempt)
public/clinics/clinic_<reg_no>_<slug>.html    one file per institution record
templates/template_clinic.html          source template ({{ placeholders }}) - not deployed
qr/                                     QR artefacts (png + data uri) - not deployed
```

## Record page — how it is built

`templates/template_clinic.html` is filled by replacing these placeholders:

| Placeholder | Meaning |
|---|---|
| `{{ name }}` | Institution / practice name shown on the dark title bar |
| `{{ photo_url }}` | Absolute photo URL, or **empty** to show the `PHOTO NOT AVAILABLE` plate |
| `{{ photo_url_class }}` | `empty` when there is no photo, otherwise blank |
| `{{ reg_no }}` | Reg No |
| `{{ licence_status }}` | Licence Status |
| `{{ licence_number }}` | Licence Number |
| `{{ valid_till }}` | Valid Till |
| `{{ practice_institution }}` | Practice Institution |
| `{{ institution_reg_no }}` | Institution Reg No |
| `{{ discipline }}` | Discipline (may be blank) |
| `{{ qualifications }}` | Qualifications (may be blank) |
| `{{ issue_date }}` | Issue Date |

Output file name: `clinics/clinic_<reg_no>_<slug>.html`, where `<slug>` is the
first distinctive word of the institution, lowercased —
`GASHE HEALTH SERVICES LIMITED` → `gashe`.

## QR

The QR encodes the **absolute record page URL** of whichever host is live. The
base URL is read from `setup_config.json` (`vercel_base_url`) by
`_gen_qr_clinic.py`, so it points at
`https://poisonsandpharmacyboard.vercel.app/clinics/clinic_007174_gashe` today.

```
python ..\_gen_qr_clinic.py <reg_no> <slug>
```

Artefacts land in `output/` (`qr_clinic_<reg_no>.png` and
`qr_clinic_<reg_no>_data_uri.txt`) and are copied into `qr/` here.

### Published records

| Institution | Reg No | Page | QR |
|---|---|---|---|
| GASHE HEALTH SERVICES LIMITED | 007174 | `public/clinics/clinic_007174_gashe.html` | `qr/clinic_007174_gashe.png` |
| RUTH MEDICAL CENTRE | 10950 | `public/clinics/clinic_10950_ruth.html` | `qr/clinic_10950_ruth.png` |

## Publishing (Vercel)

The repo is import-ready: `public/` is the deployed directory and `vercel.json`
carries the same shape as the working PPB host (clean URLs, security headers,
`/clinics/:id` rewrite).

1. <https://vercel.com/new> -> **Import Git Repository** ->
   `hezronngeno6-hue/osp.kmpdc.goke`
2. Framework preset **Other**, Root Directory `.`, Build Command empty,
   Output Directory `public` (already declared in `vercel.json`) -> **Deploy**

Every later push to `main` then deploys automatically. Add a custom domain
(e.g. `osp.kmpdc.go.ke`) under **Settings -> Domains** if you have one.

GitHub Pages is *not* used: enabling it needs a repo-admin action, and the
workflow token cannot create a Pages site
(`403 Resource not accessible by integration`).
