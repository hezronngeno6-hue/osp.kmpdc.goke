# OSP Host — `osp.kmpdc.goke`

Static replication host for the **clinic / institution licence** record page.
This repo is the deploy target: pushing to `main` publishes the site.

- Repo: <https://github.com/hezronngeno6-hue/osp.kmpdc.goke>
- Live base URL: `https://hezronngeno6-hue.github.io/osp.kmpdc.goke` *(GitHub Pages)*

## Layout

```
index.html                              portal landing page
404.html                                fallback page
.nojekyll                               serve files as-is (no Jekyll build)
clinics/clinic_<reg_no>_<slug>.html     one file per institution record
templates/template_clinic.html          the source template ({{ placeholders }})
qr/                                     generated QR artefacts (png + data uri)
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

The QR encodes the **absolute record page URL**, `.html` included, because
GitHub Pages does not guarantee extensionless paths:

```
python ..\_gen_qr_clinic.py <reg_no> <slug>
```

Artefacts land in `output/` (`qr_clinic_<reg_no>.png` and
`qr_clinic_<reg_no>_data_uri.txt`) and are copied into `qr/` here.

### Published records

| Institution | Reg No | Page | QR |
|---|---|---|---|
| GASHE HEALTH SERVICES LIMITED | 007174 | `clinics/clinic_007174_gashe.html` | `qr/clinic_007174_gashe.png` |

## Publishing

```powershell
git add -A
git commit -m "Publish clinic_007174_gashe"
git push origin main
```

GitHub Pages must be enabled once: **Settings → Pages → Source: Deploy from a
branch → `main` / `(root)`**.
