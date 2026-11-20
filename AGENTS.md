# AGENTS.md — Project memory

**Workspace:** `/media/theara/184E41A94E41810C/Theara/YEAR 3/SEMESTER 1/DSSA/Chili Midterm`
**Project:** DSS301 — Data Science for Smart Agriculture — Mid-Term Project
**Group 5:** Khon Sotheara · Khann Udomvirak · Chea Sela · Cheang Youpheng
**Status:** **COMPLETE / FROZEN** — built, executed, verified, exported, pushed to GitHub.
**Memory updated:** 20 Nov 2026.

**The deliverables describe ONE site:** the instructor's field pin at 12.140874 N, 104.0848521 E
(Damnak Trayeung, Phnum Kravanh, Pursat). No other site appears in any submitted file — that is
deliberate and must stay true.

---

## 0. TL;DR for a new session

- The DSS301 midterm is finished. Do not rebuild, re-run, rename, or "improve" the deliverables unless the user explicitly asks.
- Crop: **Red Sky 86 F1 hot chili**. Site: **the instructor's field pin, Pursat** (12.140874 N, 104.0848521 E).
- Main deliverables: `DSS301_Midterm_RedSky86_Pursat.ipynb` and `.html`, plus the deck and the speaker script.
- Headline results: **2,621 GDD** (base 10 °C) · all 10 stages **+1 to +2 days** · weather yield loss **−32.18 %** · **1.051 kg/plant** · **26,280 kg/ha** · totals **1,484.8 t** (56.5 ha) / **906.7 t** (34.5 ha).
- Data is **real NASA POWER** hourly weather, with a clearly labelled **2025 proxy** for 13/10/2026 → 02/12/2026 (NASA latency). Never describe the data as synthetic.
- The only open items are questions for the teacher (planted area, proxy acceptance), not unfinished work.

---

## 1. What the assignment was

The teacher's brief is `Midterm.jpg`. The course is **DSS301 – Data Science for Smart Agriculture**. In the teacher's words, the task was to:

1. Download weather data — air temperature, air humidity at different elevations, rainwater, soil temperature, soil humidity — for **every hour** of the day, from **0 DAT until the end of harvesting**.
2. Calculate **GDD** "for our chili plant".
3. Build a prediction model for the **exact date of each growth stage**, using the **10 growth stages in the QC data collection guidelines**.
4. Combine the stage model with the downloaded weather data.
5. Calculate the **yield loss due to weather**.
6. Calculate the **daily yield from the 1st harvest until the end of the season**, according to the plantation area.
7. Use **1 block = 0.5 ha** and **1 ha = 25,000 plants**.
8. Use **the data in the teacher-provided files** to develop the model.
9. **"Do not trend the data in AI"** — interpreted as: no black-box AI/ML, no fitting the model to the answer; use transparent, reproducible calculations instead.

All nine points are covered. The notebook's opening cells map each requirement to where it is answered.

---

## 2. Crop, variety, and site

### Crop
**Red Sky 86 F1** — an upward-growing hot chili (pepper) from Golden Coin Seeds / Tan Loc Phat Seeds.

Key supplier specs used:
- Fruit: 6.5–7.8 cm long × 1.1–1.2 cm diameter; green → red at maturity.
- Yield potential: **1.5–1.6 kg/plant** (midpoint **1.55** used).
- First harvest around **75 DAT**; season ends around **150 DAT**.
- Transplant at 0 DAT: **04/07/2026**; end of harvest: **02/12/2026**.

### Site — the instructor's pin
- Google Maps link in `Links.md`: `12°08'27.2"N 104°05'05.5"E` = **12.140874, 104.0848521**.
- Reverse geocoding: **Damnak Trayeung village, Phnum Kravanh district, Pursat province** — foothills west of the Tonle Sap, near the Cardamom edge.
- NASA POWER grid point **12.141 / 104.085**, elevation **121.88 m**.
- Why the whole analysis sits here: the brief asks for predictions *for our farm*, and this is the field the instructor pointed at. No district average and no screening of candidate provinces is used — there is no second site anywhere in the deliverables.

---

## 3. Data provenance (important — do not misstate this)

### Hourly dataset
File: `Pursat_weather_hourly_2026_season.csv`
- **3,648 rows** = 152 days × 24 hours, DAT 0 (04/07/2026) → DAT 151 (02/12/2026).
- **15 weather parameters**: `T2M`, `T10M`, `RH2M`, `QV2M`, `QV10M`, `PRECTOTCORR`, `TSOIL1`–`TSOIL6`, `GWETTOP`, `GWETROOT`, `GWETPROF`.
- Source: **NASA POWER** API, hourly, single-point, `community=AG` (Agroclimatology). Underlying product: MERRA-2 reanalysis.
- API pattern (built by `power_csv()` in the notebook):
  `https://power.larc.nasa.gov/api/temporal/hourly/point?parameters=...&community=AG&longitude=104.085&latitude=12.141&start=YYYYMMDD&end=YYYYMMDD&format=CSV`

### The proxy split
NASA POWER 2026 data was only available through **12/10/2026** when the project was built. Therefore:
- `04/07/2026 → 12/10/2026`: **actual 2026** (`source = actual_2026`).
- `13/10/2026 → 02/12/2026`: **same calendar dates from 2025**, relabelled to 2026 (`source = proxy_2025`). The `actual_datetime` column preserves the real 2025 timestamps.

This is a data-quality limitation and is stated in the notebook's findings. A new session must not remove the proxy label or claim the whole season is actual 2026.

### Reference seasons
File: `Pursat_reference_GDD_2023-2025.csv`
- Daily `T2M_MAX` / `T2M_MIN` from NASA POWER for 04/07–02/12 in **2023, 2024, 2025** at the same point.
- Used only to calibrate stage thresholds (mean cumulative GDD at each standard DAT anchor).
- The 2026 model is then driven by the actual downloaded 2026 weather.

### Soil
`Pursat_soil_lab_11-23.csv` — the instructor-supplied RUPP laboratory report (`DSSA.pdf`), **Lab ID 11/23**, fields **A1** and **A2**.

### Data download helper
`Data_Download_Links.html` — pre-filled NASA POWER deep link plus direct API links for every file the notebook uses, all pointing at this coordinate. Helper page, not part of the graded notebook/HTML.

---

## 4. Method

### 4.1 GDD
Daily GDD from hourly 2 m air temperature:

```
GDD_day = max(0, (Tmax + Tmin) / 2 − 10)
GDD_cum(t) = Σ GDD_day
```

- Base temperature: **10 °C** (standard for chili/pepper).
- Season total: **2,620.58 GDD**; mean **17.24 GDD/day**.
- Reference seasons: 2,654.77 (2023) / 2,681.42 (2024) / 2,662.20 (2025) — 2026 is **46 GDD below** the mean, a marginally cool season.

### 4.2 Stage prediction model
Logic: each stage needs a fixed amount of thermal time. Thresholds are the **mean cumulative GDD at the standard DAT anchors across 2023–2025**, applied to the actual 2026 cumulative curve; the predicted date is the first day the curve crosses the threshold.

Anchors from the teacher's agronomic standard: **7 / 30 / 45 / 60 / 75 / 150 DAT**.
The 10 stage names come from the `Stage2` column of the `Data Collection FORM A1` sheet in `Red_Sky_86_Agronomic_Growth_Standard_0_150_DAT.xlsx`:

Transplant → Establishment → Vegetative → Flowering → Fruit set → First harvest → Market fruit → Maturity → Harvest → End of cycle.

- All stages arrive **+1 to +2 days** later than the standard calendar.
- **First harvest: 18/09/2026**; **end of cycle: 04/12/2026**, flagged `extrapolated`.

**Known nuance (do not "fix" casually):** the table displays the End-of-cycle standard date as 02/12/2026, but 150 days after 04/07/2026 is 01/12/2026; the delta is computed against 01/12, and the season is treated as 152 days inclusive (DAT 0–151). 2026 reaches only 2,620.58 GDD against the calibrated 2,649 threshold, so that one date is extrapolated at the last-14-day rate and marked `extrapolated` in code, CSV and text.

**Robustness — base temperature:** re-calibrating at 8 °C and 12 °C gives the same stage dates, so the prediction does not depend on the 10 °C choice.

### 4.3 Yield loss due to weather
Potential yield: **1.55 kg/plant**. A transparent stress-index model applies penalties during the sensitive phase, **45–150 DAT**, with total loss capped at **35 %**:

| Stressor | Window | Count | Penalty per unit | Loss |
|---|---|---:|---:|---:|
| Hours >35 °C | 45–150 DAT | 0 | 0.20 % / hour | 0.00 % |
| Hours 32–35 °C | 45–150 DAT | 64 | 0.05 % / hour | 3.20 % |
| Warm nights (Tmin >24 °C) | 45–75 DAT | 16 | 0.30 % / night | 4.80 % |
| Hours RH ≥90 % (disease pressure) | 45–150 DAT | 1,209 | 0.02 % / hour | 24.18 % |
| Days >50 mm rain | 45–150 DAT | 0 | 0.50 % / day | 0.00 % |
| Dry-spell days beyond 7 | 40–90 DAT | 0 | 0.30 % / day | 0.00 % |
| **Total** | | | | **−32.18 %** |

- **Achieved yield:** 1.55 × (1 − 0.3218) = **1.051 kg/plant**.
- **Per hectare:** 25,000 × 1.051 = **26,280 kg/ha**.

**Proxy transparency:** of the 1,209 RH ≥ 90 % hours, **686 are actual 2026** and **523 come from the 2025 proxy** — **10.46 of the 32.18 loss points (≈ 33 %)**. The remaining 21.72 points are driven by real 2026 weather. This breakdown is printed in the notebook and must be stated honestly.

### 4.4 Daily attainable yield
- Harvest window: **18/09/2026 → 04/12/2026** = **77 daily rows**.
- Attainable yield = plants × achieved kg/plant, distributed with a smooth **bell-shaped picking curve** (peak mid-season; `sigma = n/5`; weights normalised to sum to 1).
- Density: **25,000 plants/ha** as required by the brief.
- Two area options, because the teacher's materials allow two interpretations:
  - **113 blocks** (Zone A: A1–A66 = 66; Zone B: B1–B47 = 47) × 0.5 ha = **56.5 ha** → 1,412,500 plants.
  - **69 planted plots** × 0.5 ha = **34.5 ha** → 862,500 plants.

**Daily yield results:**
- Peak: **680 kg/ha/day on 26/10/2026**.
- Season total: **1,484.8 t** for 56.5 ha; **906.7 t** for 34.5 ha.

### 4.5 Sensitivity
- ±50 % on all loss penalties: the attained yield stays within **1.01–1.30 kg/plant** (the upper bound plateaus at the 35 % cap).
- This is slide 10 of the deck; the conclusion "humidity, not heat, costs the yield" holds across the whole range.

---

## 5. Other findings worth remembering

- **Zero hours above 35 °C** in the whole season, and zero days above 50 mm rain; the heat, heavy-rain and dry-spell channels are all empty.
- Root zone (`GWETROOT`) mean **0.70**, minimum **0.55** — never water-limited, never long waterlogged.
- Surface wetness (`GWETTOP`) mean 0.71; 482 hours >0.8 but only 5 hours >0.9 — moderate, well below a waterlogging problem.
- Soil temperatures: `TSOIL1` (0–10 cm) mean 27.0 / max 33.9 °C; `TSOIL2` (10–40 cm) 27.2 / 30.1 °C — stable, near-ideal root zone.
- Air temperature gradient `T2M − T10M`: mean **−0.114 °C**; humidity gradient `QV2M − QV10M`: **0.530 g/kg** (moisture concentrated near the canopy).
- The structural risk at this site is **humidity-driven disease**, not heat, rain or drought — the exact channel that dominates the loss model.
- Soil (lab 11/23): both fields **silt loam**, pH **7.16 / 7.11** against a **5.0–6.5** chili optimum, available P **155 / 173 ppm** (very high — do not add phosphate), CEC **18.4 / 18.5**, base saturation **65 % / 78 %**, AWC **90 mm**. A2 is the stronger field (higher BS, K almost 3×).

---

## 6. Deliverables and file inventory

### 6.1 Analysis outputs
| File | What it is |
|---|---|
| `DSS301_Midterm_RedSky86_Pursat.ipynb` | Main executed notebook, 33 cells / 19 code cells, 0 errors, Group 5 on title |
| `DSS301_Midterm_RedSky86_Pursat.html` | Submission export with every output embedded |
| `Pursat_weather_hourly_2026_season.csv` | Hourly NASA POWER dataset, 3,648 rows, 15 parameters |
| `Pursat_GDD_daily_2026.csv` | Daily Tmax/Tmin/Tmean, GDD/day and cumulative |
| `Pursat_reference_GDD_2023-2025.csv` | Three reference seasons for threshold calibration |
| `Pursat_stage_predictions.csv` | 10-stage table: standard vs predicted dates |
| `Pursat_daily_yield.csv` | 77 daily rows, kg/ha and totals for both area options |
| `Pursat_soil_lab_11-23.csv` | Laboratory results for fields A1 and A2 |
| `fig_pursat_gdd_timeline.png` · `fig_pursat_daily_yield.png` · `fig_pursat_gdd_seasons.png` · `fig_pursat_soil_ph.png` | Report figures |

### 6.2 Presentation materials
| File | What it is |
|---|---|
| `DSS301_RedSky86_Pursat_Slides.pptx` | 13-slide 16:9 deck, dark theme |
| `PRESENTATION_SCRIPT.md` | 13 min + 5 min Q&A, split across the four presenters |
| `banner.png`, `fig_slide_loss.png` | README banner and slide chart (matplotlib, not analysis outputs) |

### 6.3 Teacher-provided reference files (keep, never delete)
| File | Notes |
|---|---|
| `Midterm.jpg` | The brief (photo of the teacher's requirements) — says "our chili plant", 1 block = 0.5 ha, 1 ha = 25,000 plants |
| `Red_Sky_86_Agronomic_Growth_Standard_0_150_DAT.xlsx` / `.pdf` | 4 sheets: `Red Sky 86 Standard`, `Data Collection FORM`, `Data Collection FORM A1`, `Overall Data-Input` |
| `DATA_COLLECTION_FORM_RED_SKY_86_F1_…xlsx` | Blank data-collection form |
| `PLANTING GUIDE OF F1 HOT CHILI SEEDS – RED SKY 86.pdf` | Supplier guide (yield 1.5–1.6 kg/plant, fruit size, diseases, 75-day harvest) |
| `Chili_QA_QC_Laminated_Field_Measurement_Guide.pdf` | QA/QC measurement protocol — **not** the stage list |
| `DSSA.pdf` | Soil lab report, Lab ID 11/23, fields A1/A2 |
| `Soils_and_Crop_Suitability_06.11.2026.pdf`, `Soil_Science_Source_Chapters_2021.pdf`, `An Introduction to Precision Agriculture.pdf` | Course reading |
| `QA_QC-Management.xlsx`, `QA_QC-Management_DNTY-UKMAC (1).xlsx`, `DSS301_Week4.pdf`, `DSS301_Week4_Group_Worksheet.docx` | Week 4 material (separate activity) |
| `photo_2026-10-03_12-21-56.jpg`, `…-12-22-00.jpg`, `…-12-22-04.jpg` | Khmer field slides (Zone A/B maps, fertiliser/pesticide zones) |
| `Dataset Website.PNG` | Screenshot of the NASA POWER Data Access Viewer |
| `Links.md` | Source links — **bare links only, no text** (user requirement) |

---

## 7. Notebook structure (33 cells, 19 code cells)

| Section | Content |
|---|---|
| Title | Group 5, requirements map, constants, data source, headline results |
| 1 | The field — the instructor's location, why the analysis sits there |
| 2 | Weather dataset: parameters, proxy note, QC (0 missing / 0 duplicate / 0 invalid) |
| 3 | GDD definition and formula; daily aggregation writes `Pursat_GDD_daily_2026.csv` |
| 4 | Soil and microclimate table (soil temp, wetness, gradients) |
| 5 | Stage model: reference thresholds, predicted vs standard dates, writes `Pursat_stage_predictions.csv`; base-temperature robustness |
| 6 | Loss model: six channels, proxy transparency, total −32.18 % |
| 7 | Daily attainable yield, two area options, writes `Pursat_daily_yield.csv`; sensitivity |
| 8 | Soil suitability from the lab report, writes `Pursat_soil_lab_11-23.csv` |
| 9 | Figures (4) |
| 10 | Findings and limitations |
| 11 | Output-file table |

The notebook is **idempotent**: it reloads the cached CSVs when present; if the hourly CSV is missing, `build_hourly()` downloads from NASA POWER again.

---

## 8. Build and verification details

### Environment
- Python 3 with `pandas`, `numpy`, `matplotlib`, `nbformat`, `nbconvert`, `jupyter`, `openpyxl`.
- Deck work needs `python-pptx`, installed with `pip install --target .pylibs python-pptx` and run as `PYTHONPATH=.pylibs python3 …` (`.pylibs/` is gitignored).
- `soffice` + `pdftoppm` are used to render slides to PNG for visual checks.

### Verify command
```
jupyter nbconvert --to notebook --execute --inplace DSS301_Midterm_RedSky86_Pursat.ipynb
jupyter nbconvert --to html DSS301_Midterm_RedSky86_Pursat.ipynb
```

**Do not run this unless the user asks.** Rebuilding overwrites the frozen notebook, CSVs and HTML; if NASA has since released more actual 2026 data, results could shift and the group would have to resend. The current results are the delivered ones.

### Verification already performed
- Full execution: 0 errors, 19 code cells with outputs, 33 cells.
- All four figures render; HTML export carries every output.
- All 13 slides render-checked through LibreOffice → PNG.
- Every text reference to another site removed from notebook, HTML, README, deck, script and link page.

---

## 9. Rules and user preferences for future sessions

1. **Keep the teacher's files.** Never delete `Midterm.jpg`, the PDFs, the XLSX/DOCX files, the Khmer photos, or `Dataset Website.PNG`.
2. **One site only.** Every submitted file describes the instructor's Pursat pin. Do not reintroduce any other location, comparison table or screening discussion — the user asked for exactly this and it was cleaned out on 20 Nov 2026.
3. **No AI/ML / "trending".** The analysis must stay a transparent, deterministic math pipeline. Every number must be traceable to the CSVs and formulas.
4. **Real data only.** The weather is real NASA POWER/MERRA-2 data. The 2025 proxy is clearly labelled. Never call the dataset synthetic.
5. **Be precise with numbers and paths,** and short in prose — the user is impatient with long output and wants direct answers.
6. **Do not invent more work.** The project is done. The remaining items are questions for the teacher, not tasks.
7. **`Links.md` stays bare links** — no commentary. **AGENTS.md** stays the memory file.
8. **Group 5 naming:** Khon Sotheara · Khann Udomvirak · Chea Sela · Cheang Youpheng. No student IDs (none were provided).

---

## 10. Open items for the teacher (not unfinished work)

1. **Planted area:** the materials allow **113 blocks = 56.5 ha** (Zone A 66 + Zone B 47) or **69 planted plots = 34.5 ha**. Both totals are computed: **1,484.8 t** vs **906.7 t**.
2. **Proxy acceptance:** 13/10 → 02/12/2026 uses 2025 same-date weather because NASA POWER's 2026 data ended 12/10/2026. About 10.5 of the 32.18 loss points depend on it.
3. **Coefficient assumptions:** the stress penalties are simplified exercise assumptions, not fitted field coefficients; the ±50 % sensitivity shows the range.
4. **End-of-cycle nuance:** 150 DAT after 04/07/2026 is 01/12/2026; the season ends 02/12/2026 (DAT 151) and the predicted date is extrapolated to 04/12/2026.
5. **Student IDs:** only names are on the title; add IDs only if required.

---

## 11. Quick reference — key numbers

| Item | Value |
|---|---|
| Planting / 0 DAT | 04/07/2026 |
| End of harvest | 02/12/2026 (DAT 151) |
| Season length | 152 days inclusive |
| Base temperature | 10 °C |
| Season GDD | 2,620.58 |
| Reference seasons | 2,654.77 / 2,681.42 / 2,662.20 |
| Mean GDD/day | 17.24 |
| Stage thresholds (GDD) | 146 / 562 / 836 / 1,105 / 1,370 / 2,649 |
| Stage accuracy | all +1 to +2 days |
| Potential yield | 1.55 kg/plant |
| Weather loss | −32.18 % (cap 35 %) |
| Attained yield | 1.051 kg/plant |
| Yield per ha | 26,280 kg/ha |
| Harvest window | 18/09/2026 → 04/12/2026 (77 days) |
| Peak daily yield | 680 kg/ha/day on 26/10/2026 |
| Total, 56.5 ha (113 blocks) | 1,484.8 t |
| Total, 34.5 ha (69 plots) | 906.7 t |
| Plants, 56.5 ha | 1,412,500 |
| Plants, 34.5 ha | 862,500 |
| Actual weather | 04/07/2026 → 12/10/2026 |
| Proxy weather | 13/10/2026 → 02/12/2026 (2025 same dates) |
| Proxy share of loss | 10.46 of 32.18 points (~33 %) |

---

## 12. Repository, assets and environment notes

### 12.1 GitHub repository

- **Public repo:** `github.com/ThearaGithub/dss301-chili-midterm` (owner `ThearaGithub`), branch `main`.
- The local repo lives in the project folder itself; commits are authored as `ThearaGithub <ThearaGithub@users.noreply.github.com>`, no AI footer.
- Push works through the machine's **SSH key**, already authenticated to GitHub. There is **no `gh` CLI** and no token, so repo *creation* had to be done in the browser — pushing needs no login.
- Content is **this site only**. Course PDFs, the duplicate QA/QC workbook, the field photos and the `Week4` files are excluded via `.gitignore`. `banner.png` needs the explicit `!banner.png` negation because a broad `*.png` rule would swallow it.
- `README.md` is written as a report (banner + results + method + stated limitations + figures), not as a file list.

### 12.2 Presentation assets

- `DSS301_RedSky86_Pursat_Slides.pptx` — **13 slides, 16:9**, dark theme (`#0F1D2E` bg, `#E8A33D` accent, `#4FB3A5` teal), built with `python-pptx` and render-checked slide by slide through LibreOffice.
- Slide 10 is **sensitivity** (1.01–1.30 kg/plant, ±2 days stage timing), not a site comparison.
- `PRESENTATION_SCRIPT.md` — **13 min + 5 min Q&A**: Sotheara 1–4, Udomvirak 5–6, Sela 7–8, Youpheng 9–12, all four for the close, plus a six-answer Q&A bank.

### 12.3 Data-source decision (settled — do not reopen casually)

Only **two** sources feed the numbers: NASA POWER (weather) and the instructor's lab report (soil).

- **NASA POWER stays primary** because it provides `GWETROOT` (root-zone wetness) directly, needs no key, and serves every brief parameter in one call.
- **ERA5-Land (~9 km) is the stated cross-check, not a substitute.** Tested at the same point: GDD 2,492 vs NASA 2,621, mean T 25.87 vs 26.84 °C, **hours >35 °C = 0 in both**, hours RH ≥ 90 % **2,014 vs 1,209**, mean RH 88.07 vs 83.68 %. The heat channel is robust; the humidity channel is where the models diverge, and ERA5-Land's wetter bias would push loss up.
- **Open-Meteo** serves all six channel inputs in one call and is the only realistic alternative primary if a sharper grid is ever wanted — but that means a full rebuild.
- **SoilGrids** resolves but is unreachable from this machine (HTTP 000). **CamSIS** has no public API. Neither is used.
- Blending/averaging sources was **rejected on purpose**: the loss model is threshold-based, so an averaged dataset yields a loss that matches no real record and cannot be marked.
- The single most valuable addition would be a **local rain gauge / field logger** — no reanalysis resolves one field at 55 km.

### 12.4 What is assumed rather than measured (be ready to defend)

1. **33 % of the weather is a labelled 2025 proxy** (13/10 → 02/12/2026) — carries ~10.5 of the 32.18 loss points.
2. **End-of-cycle date is extrapolated** (2,620.58 GDD reached vs 2,649 threshold).
3. **Yield settings are assumptions**: 1.55 kg/plant potential, 25,000 plants/ha, the six penalty coefficients, 35 % cap.
4. **Soil water 90 mm** = assumed AWC 0.20 × rooting depth 0.45 m, not measured.
5. **Block count is ours, the area rule is hers.** The brief states "1 block = 0.5 ha", so 113 blocks → 56.5 ha and 69 plots → 34.5 ha; the block counts themselves were not surveyed. Tonnages are therefore indicative; per-hectare figures are unaffected. This is the weakest link if a marker probes.

### 12.5 Environment gotchas

- The drive is **NTFS via `ntfs3`**; permission bits come from Windows ACLs. `chmod u+w` on a parent folder is the fix when a write fails with `EACCES` on an NTFS path.
- Python is **externally managed (PEP 668)**: `pip install` needs `--target`, and `python3 -m venv` fails (no `ensurepip`). Working trick: `pip install --target .pylibs python-pptx` then `PYTHONPATH=.pylibs`.
- `pandas`, `numpy`, `matplotlib`, `jupyter`/`nbconvert`, `soffice`, `pdftoppm` are available; `python-docx` is not (docx read via `zipfile` + XML).
- Scratch scripts live in `/tmp` and are removed afterwards.

### 12.6 Still outstanding (not blockers)

- `DSS301_Week4_Group_Worksheet.docx` is **blank** — a separate Week 4 group activity, not part of the midterm notebook. Mission B needs the **fictional Takeo 1–14 Aug 2026 file, which is not in the folder**.
- The real planted area for this field, if it is not 56.5 ha.

---

## 13. Change log

- **2026-11-18** — this `AGENTS.md` created from the old session export; the 3.2 MB session dump was deleted.
- **2026-11-18** — project folder renamed `IDK` → `Chili Midterm`.
- **2026-11-18** — new instructor files received (Week 4 lecture + worksheet, precision-agriculture deck, soil lesson + source chapters, soil lab report `DSSA.pdf`, QA/QC spreadsheets, Copernicus/SoilGrids/CamSIS links, and the Google Maps field pin).
- **2026-11-18** — full analysis for the instructor's pin: NASA POWER hourly + 3 reference seasons downloaded for 12.1409 N / 104.0849 E, all CSVs and 4 figures written, notebook executed and exported to HTML.
- **2026-11-18** — data-source audit: NASA kept as primary; ERA5-Land tested as cross-check; Open-Meteo verified as the only full-coverage alternative; SoilGrids unreachable; CamSIS API-less. Averaging rejected.
- **2026-11-18** — GitHub repo created (`ThearaGithub/dss301-chili-midterm`, public, `main`), README written as a report, three commits pushed over SSH.
- **2026-11-18** — 13-slide deck and 4-speaker script added; README banner and slide chart generated; all slides render-checked.
- **2026-11-18** — `/export` fixed by `chmod u+w` on the parent folder; scratch files in `/tmp` cleaned up.
- **2026-11-20** — **removed every reference to the earlier site**: the comparison section of the notebook deleted (33 cells now), sections renumbered, HTML re-exported; slide 2/3 wording changed; slide 10 rebuilt as a sensitivity slide; README figure table, script and `Data_Download_Links.html` repointed to this coordinate; this memory file rewritten the same way.
- **2026-11-20** — the earlier site's local copies were also removed from the folder; only this site's files remain.
