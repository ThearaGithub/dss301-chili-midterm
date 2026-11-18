# AGENTS.md — Project memory

**Workspace:** `C:\Theara\YEAR 3\SEMESTER 1\DSSA\Chili Midterm`
**Project:** DSS301 — Data Science for Smart Agriculture — Mid-Term Project
**Group 5:** Khon Sotheara · Khann Udomvirak · Chea Sela · Cheang Youpheng
**Status:** **COMPLETE / FROZEN** — built, executed, verified, exported, and already sent to the group.
**Memory updated:** 18 Nov 2026 (this file replaces the old session export dump).

---

## 0. TL;DR for a new session

- The DSS301 midterm is finished. Do not rebuild, re-run, rename, or "improve" the deliverables unless the user explicitly asks.
- Crop: **Red Sky 86 F1 hot chili**. Site: **Kampot, Cambodia** (lat 10.61, lon 104.18).
- Main deliverables: `DSS301_Midterm_RedSky86_Kampot.ipynb` and `DSS301_Midterm_RedSky86_Kampot.html`.
- Headline results: **2,685 GDD** (base 10 °C) · all 10 stages within **±1 day** · weather yield loss **−27.86%** · **1.118 kg/plant** · **27,954 kg/ha** · season total **1,579.4 t** (56.5 ha) or **964.4 t** (34.5 ha).
- Data is **real NASA POWER** hourly weather, with a clearly labelled **2025 proxy** for 13/10/2026 → 02/12/2026 (NASA latency). Never describe the data as synthetic.
- The user already sent the files to the group and does not want to resend. Treat everything as frozen; only `Data_Download_Links.html` changed after the final build.
- The only open items are questions for the teacher (plantation area and proxy acceptance), not unfinished work.

---

## 0b. Pursat branch — added 18 Nov 2026

After the Kampot deliverables were submitted, the instructor supplied a **field location** (Google Maps link in `Links.md`): `12°08'27.2"N 104°05'05.5"E` = **12.140874, 104.0848521** → **Damnak Trayeung village, Phnum Kravanh district, Pursat province**. The user asked for the whole analysis to be redone for that field, plus the newly supplied soil lab report.

- New deliverables: `DSS301_Midterm_RedSky86_Pursat.ipynb` + `.html` (35 cells, executed, 4 figures).
- New data files: `Pursat_weather_hourly_2026_season.csv`, `Pursat_GDD_daily_2026.csv`, `Pursat_reference_GDD_2023-2025.csv`, `Pursat_stage_predictions.csv`, `Pursat_daily_yield.csv`, `Pursat_soil_lab_11-23.csv`.
- Method is **identical** to the Kampot run (GDD base 10 °C, 3-season threshold calibration, same six loss channels and coefficients, same bell-shaped yield distribution, same area options), so the two sites are directly comparable.
- Headline Pursat results: **2,621 GDD** · stages **+1 to +2 days** · loss **−32.18 %** · **1.051 kg/plant** · **26,280 kg/ha** · **1,484.8 t** (56.5 ha) / **906.7 t** (34.5 ha).
- The end-of-cycle threshold (2,649 GDD) is **not reached** inside the window; that one date is extrapolated at the last-14-day rate and flagged `extrapolated` in the code and text.
- Soil: instructor report `DSSA.pdf`, **Lab ID 11/23**, fields **A1/A2**, both **silt loam**, pH **7.16 / 7.11** (above the 5.0–6.5 chili optimum), available P **155 / 173 ppm** (very high), CEC **18.4 / 18.5**. A2 is the better field (BS 78 %, K 1.98).
- The Kampot notebook/HTML are **still frozen and untouched**; the Pursat work is additive.

---

## 1. What the assignment was

The teacher's brief is `Midterm.jpg` (a photo of the requirements). The course is **DSS301 – Data Science for Smart Agriculture**. The task, in the teacher's words, was to:

1. Download weather data — air temperature, air humidity at different elevations, rainwater, soil temperature, soil humidity — for **every hour** of the day, from **0 DAT until the end of harvesting**.
2. Calculate **GDD** for the chili plant.
3. Build a prediction model for the **exact date of each growth stage**, using the **10 growth stages in the QC data collection guidelines**.
4. Combine the stage model with the downloaded weather data.
5. Calculate the **yield loss due to weather**.
6. Calculate the **daily yield from the 1st harvest until the end of the season**, according to the plantation area.
7. Use **1 block = 0.5 ha** and **1 ha = 25,000 plants**.
8. Use **the data in the teacher-provided files** to develop the model.
9. **"Do not trend the data in AI"** — interpreted as: no black-box AI/ML, no fitting the model to the answer; use transparent, reproducible calculations instead.

All nine points are covered. The notebook's Section 1 title cell lists the requirements and the mapping.

---

## 2. Crop, variety, and site decisions

### Crop
**Red Sky 86 F1** — an upward-growing hot chili (pepper) from Golden Coin Seeds / Tan Loc Phat Seeds.

Key supplier specs used:
- Fruit: 6.5–7.8 cm long × 1.1–1.2 cm diameter; green → red at maturity.
- Yield potential: **1.5–1.6 kg/plant** (midpoint **1.55** used).
- First harvest around **75 DAT**; season ends around **150 DAT**.
- Transplant at 0 DAT: **04/07/2026**; end of harvest: **02/12/2026**.

### Site
**Kampot, Cambodia — 10.61° N, 104.18° E, elevation 34.33 m.**

No farm location was given, so 10 candidate provinces were screened over three reference seasons (04/07–02/12, 2023–2025) for chili suitability: share of hours in the 20–30 °C optimum, heat-stress hours (>35 °C), rainfall, humidity/disease pressure.

Top of the screening table:

| Site | Score | Mean T (°C) | Hours >35 °C | Hours 20–30 °C | Rain (mm) | Rainy days | Mean RH |
|---|---:|---:|---:|---:|---:|---:|---:|
| **Kampot (selected)** | **0.758** | 27.5 | **0** | **95%** | 1,327 | 127 | 86% |
| Pursat | 0.755 | 27.0 | 0 | 83% | 971 | 116 | 84% |
| Battambang | 0.733 | 26.9 | 0 | 83% | 1,190 | 114 | 87% |
| Takeo | 0.729 | 27.2 | 3 | 84% | 1,124 | 113 | 87% |
| Siem Reap | 0.710 | 27.9 | 26 | 72% | 568 | 107 | 80% |
| Phnom Penh | 0.696 | 27.5 | 38 | 79% | 887 | 108 | 84% |

**Why Kampot won:** zero heat-stress hours in three seasons and 95% of hours inside the optimal temperature range. Its higher rain/humidity means more disease pressure than Pursat, but that is manageable with the farm spray programme and is explicitly modelled through the RH ≥ 90% loss channel.

---

## 3. Data provenance (important — do not misstate this)

### Hourly dataset
File: `Kampot_weather_hourly_2026_season.csv`
- **3,648 rows** = 152 days × 24 hours, DAT 0 (04/07/2026) → DAT 151 (02/12/2026).
- **15 weather parameters**: `T2M`, `T10M`, `RH2M`, `QV2M`, `QV10M`, `PRECTOTCORR`, `TSOIL1`–`TSOIL6`, `GWETTOP`, `GWETROOT`, `GWETPROF`.
- Source: **NASA POWER** API, hourly, single-point, `community=AG` (Agroclimatology).
- API pattern (built by `power_csv()` in the notebook):
  `https://power.larc.nasa.gov/api/temporal/hourly/point?parameters=...&community=AG&longitude=104.18&latitude=10.61&start=YYYYMMDD&end=YYYYMMDD&format=CSV`
- Underlying product: NASA **MERRA-2** reanalysis, served by POWER.

### The proxy split
NASA POWER data for 2026 was only available through **12/10/2026** when the project was built. Therefore:
- `04/07/2026 → 12/10/2026`: **actual 2026** (`source = actual_2026`).
- `13/10/2026 → 02/12/2026`: **same calendar dates from 2025**, relabelled to 2026 (`source = proxy_2025`). The `actual_datetime` column preserves the real 2025 timestamps.

This is a data-quality limitation and is stated in the notebook's findings. A new session must not silently remove the proxy label or claim the whole season is actual 2026.

### Reference seasons
File: `Kampot_reference_GDD_2023-2025.csv`
- Daily `T2M_MAX` / `T2M_MIN` from NASA POWER for 04/07–02/12 in **2023, 2024, 2025**.
- Used only to calibrate stage thresholds (the mean cumulative GDD at each standard DAT anchor).
- The 2026 model is then driven by the actual downloaded 2026 weather.

### Data download helper
`Data_Download_Links.html` contains:
- A pre-filled NASA POWER Data Access Viewer deep link (single-point, hourly, AG, Kampot, 04/07/2026–12/10/2026).
- Five direct dataset/API links, all re-verified HTTP 200.
- This is a **helper page**, not part of the graded notebook/HTML. It was rewritten on 18 Oct 2026, after the final notebook build. The user said they would still use the provided data ("ill use yours still"), so do not assume the helper is part of the submission.

---

## 4. Method

### 4.1 GDD
Daily GDD from hourly 2 m air temperature:

```
GDD_day = max(0, (Tmax + Tmin) / 2 − 10)
GDD_cum(t) = Σ GDD_day
```

- Base temperature: **10 °C** (standard for chili/pepper).
- Season total: **2,685 GDD**; mean **17.7 GDD/day**.
- Very close to the 2023–2025 Kampot average, so 2026 is a climatically normal season for the site.

### 4.2 Stage prediction model
Logic: each stage needs a fixed amount of thermal time. Thresholds are the **mean cumulative GDD at the standard DAT anchors across 2023–2025**. Those thresholds are then applied to the actual 2026 cumulative GDD curve; the predicted stage date is the first day the curve crosses the threshold.

Anchors from the teacher's agronomic standard: **7 / 30 / 45 / 60 / 75 / 150 DAT**.
The 10 stage names come from the `Stage2` column of the `Data Collection FORM A1` sheet in `Red_Sky_86_Agronomic_Growth_Standard_0_150_DAT.xlsx`:

Transplant → Establishment → Vegetative → Flowering → Fruit set → First harvest → Market fruit → Maturity → Harvest → End of cycle.

**Final stage table:**

| # | Stage | Anchor DAT | Standard date | GDD threshold | Predicted date | Predicted DAT | Delta |
|---:|---|---:|---|---:|---|---:|---:|
| 1 | Transplant | 0 | 04/07/2026 | 0 | 04/07/2026 | 0 | 0 |
| 2 | Establishment | 7 | 11/07/2026 | 146 | 11/07/2026 | 7 | 0 |
| 3 | Vegetative | 30 | 03/08/2026 | 562 | 04/08/2026 | 31 | +1 |
| 4 | Flowering | 45 | 18/08/2026 | 836 | 19/08/2026 | 46 | +1 |
| 5 | Fruit set | 60 | 02/09/2026 | 1,105 | 02/09/2026 | 60 | 0 |
| 6 | First harvest | 75 | 17/09/2026 | 1,370 | 17/09/2026 | 75 | 0 |
| 7 | Market fruit | 75 | 17/09/2026 | 1,370 | 17/09/2026 | 75 | 0 |
| 8 | Maturity | 75+ | starts 17/09/2026 | — | 17/09/2026 | 75 | — |
| 9 | Harvest | 75–150 | 17/09/2026–02/12/2026 | — | 17/09/2026–02/12/2026 | — | — |
| 10 | End of cycle | 150 | 02/12/2026 (table display) | 2,673 | 02/12/2026 | 151 | +1 |

All stages are within **±1 day** of the standard calendar.

**Known nuance (do not "fix" casually):** The table displays the End-of-cycle standard date as 02/12/2026, but 150 days after 04/07/2026 is 01/12/2026. The +1 delta is computed against 01/12. The season is treated as 152 days inclusive (DAT 0–151), ending 02/12. This is consistent across the notebook and CSVs. Do not change it without the user's approval — the files were already submitted.

**Robustness — base temperature:** re-calibrating and re-predicting with 8 °C and 12 °C gives the same stage dates. The prediction does not depend on the 10 °C choice.

### 4.3 Yield loss due to weather
Potential yield: **1.55 kg/plant** (midpoint of the supplier's 1.5–1.6). A transparent stress-index model applies penalties during the sensitive phase, **45–150 DAT**, with total loss capped at **35%**:

| Stressor | Window | Count in 2026 | Penalty per unit | Loss |
|---|---|---:|---:|---:|
| Hours >35 °C | 45–150 DAT | 0 | 0.20% / hour | 0.00% |
| Hours 32–35 °C | 45–150 DAT | 0 | 0.05% / hour | 0.00% |
| Warm nights (Tmin >24 °C) | 45–75 DAT | 31 | 0.30% / night | 9.30% |
| Hours RH ≥90% (disease pressure) | 45–150 DAT | 878 | 0.02% / hour | 17.56% |
| Days >50 mm rain | 45–150 DAT | 2 | 0.50% / day | 1.00% |
| Dry-spell days beyond 7 | 40–90 DAT | 0 | 0.30% / day | 0.00% |
| **Total** | | | | **−27.86%** |

- **Achieved yield:** 1.55 × (1 − 0.2786) = **1.118 kg/plant**.
- **Per hectare:** 25,000 × 1.118 = **27,954 kg/ha**.

**Proxy transparency:** of the 878 RH ≥ 90% hours, **519 are from the 2025 proxy** (the other 359 are actual 2026). That proxy-driven component is **10.38 percentage points** of the 27.86% loss, or about **37% of the total loss**. The warm nights and heavy-rain days are all actual 2026. So roughly **17.5 points are actual-2026-driven** and **10.4 points are proxy-driven**. This breakdown is printed in the notebook and must be stated honestly.

### 4.4 Daily attainable yield
- Harvest window: predicted first harvest **17/09/2026** → end of cycle **02/12/2026** = **77 daily rows**.
- Attainable yield = plants × achieved kg/plant, distributed over the window with a smooth **bell-shaped picking curve** (peak mid-season; `sigma = n/5`; weights normalised to sum to 1).
- Density: **25,000 plants/ha** as required by the brief.
- Two area options are computed because the teacher's materials show two possible interpretations:
  - **113 blocks** (Zone A: A1–A66 = 66; Zone B: B1–B47 = 47) × 0.5 ha = **56.5 ha** → 1,412,500 plants.
  - **69 planted plots** × 0.5 ha = **34.5 ha** → 862,500 plants.

**Daily yield results:**
- Peak: **733.3 kg/ha/day on 25/10/2026**.
- Season total: **1,579.4 t** for 56.5 ha; **964.4 t** for 34.5 ha.
- Average daily output: **20,512 kg/day** (56.5 ha); **12,525 kg/day** (34.5 ha).

### 4.5 Sensitivity
- ±50% on all loss penalties:
  - Optimistic (0.5×): loss **13.93%**, yield **1.334 kg/plant**, about 33,357 kg/ha.
  - Central: loss **27.86%**, yield **1.118 kg/plant**, 27,954 kg/ha.
  - Pessimistic (1.5×): loss capped at **35%**, yield **1.008 kg/plant**, 25,197 kg/ha.
- So the attained yield stays within roughly **1.01–1.33 kg/plant** under the coefficient assumptions.

---

## 5. Other findings worth remembering

- Kampot 2026 is thermally near-ideal: **zero hours >35 °C** and zero hours 32–35 °C in the season.
- Soil root zone (`GWETROOT`) mean **0.85**, minimum **0.68** — never water-limited; **75.7% of hours >0.8** (near saturation).
- Surface wetness (`GWETTOP`): **79% of hours >0.8**, 891 hours >0.9 — high waterlogging/disease risk.
- Soil temperatures: `TSOIL1` (0–10 cm) 19.9–30.8 °C, mean 26.7; `TSOIL2` (10–40 cm) 22.8–28.4 °C, mean 26.9 — stable, near-ideal root zone.
- Air temperature gradient `T2M − T10M`: mean **−0.09 °C**; humidity gradient `QV2M − QV10M`: **+0.49 g/kg** (strong near-ground evapotranspiration/condensation environment).
- The site's structural risk is **excess water / root disease, not drought**; this corroborates the RH-driven disease-loss channel and the zero dry-spell penalty.
- The planting guide's main rule — fields must be **well drained and airy** — matches this finding.

---

## 6. Deliverables and file inventory

Folder: `C:\Theara\YEAR 3\SEMESTER 1\DSSA\Chili Midterm`

### 6.1 Graded deliverables (10 files)
| File | Size (bytes) | What it is |
|---|---:|---|
| `DSS301_Midterm_RedSky86_Kampot.ipynb` | 343,382 | Main executed notebook, 29 cells / 17 code cells, 0 errors, Group 5 on title |
| `DSS301_Midterm_RedSky86_Kampot.html` | 706,966 | Submission export with every output embedded |
| `Kampot_weather_hourly_2026_season.csv` | 478,120 | Hourly NASA POWER dataset, 3,648 rows, 15 parameters |
| `Kampot_GDD_daily_2026.csv` | 12,489 | Daily Tmax/Tmin/Tmean, GDD/day and cumulative |
| `Kampot_reference_GDD_2023-2025.csv` | 20,383 | Three reference seasons for threshold calibration |
| `Kampot_stage_predictions.csv` | 749 | 10-stage table: standard vs predicted dates |
| `Kampot_daily_yield.csv` | 8,415 | 77 daily rows, kg/ha and totals for both area options |
| `fig_gdd_timeline.png` | 140,547 | Temperature, cumulative GDD, stage dates |
| `fig_daily_yield.png` | 54,154 | Daily + cumulative yield curve |
| `fig_gdd_seasons.png` | 70,888 | 2026 vs 2023–2025 cumulative GDD |

### 6.2 Teacher-provided reference files (10 files — keep, never delete)
| File | Notes |
|---|---|
| `Midterm.jpg` | The brief (photo of the teacher's requirements) |
| `Red_Sky_86_Agronomic_Growth_Standard_0_150_DAT.xlsx` | 4 sheets: `Red Sky 86 Standard`, `Data Collection FORM`, `Data Collection FORM A1`, `Overall Data-Input` |
| `Red_Sky_86_Agronomic_Growth_Standard_0_150_DAT.pdf` | Printable version of the standard |
| `DATA_COLLECTION_FORM_RED_SKY_86_F1_-_AGRONOMIC_GROWTH_STANDARD_0-150.xlsx` | Blank data-collection form |
| `PLANTING GUIDE OF F1 HOT CHILI SEEDS - RED SKY 86.pdf` | Supplier agronomic guide (yield 1.5–1.6 kg/plant, fruit size, diseases, 75-day harvest) |
| `Chili_QA_QC_Laminated_Field_Measurement_Guide.pdf` | QA/QC measurement protocol — **not** the stage list |
| `photo_2026-10-03_12-21-56.jpg` | Khmer field slides (Zone A/B maps, fertiliser/pesticide zones) |
| `photo_2026-10-03_12-22-00.jpg` | Khmer field slide |
| `photo_2026-10-03_12-22-04.jpg` | Khmer field slide |
| `Dataset Website.PNG` | Screenshot of the NASA POWER Data Access Viewer |

### 6.3 Helper + memory
| File | Notes |
|---|---|
| `Data_Download_Links.html` | Helper with the DAV deep link + 5 direct API links. Changed after the final build; not part of the graded notebook/HTML. |
| `AGENTS.md` | This memory file. |

After the cleanup: **22 files** total — 10 graded deliverables, 10 teacher references, 1 helper page, 1 memory file. No `.py` files remain in the project folder.

---

## 7. Notebook structure (29 cells, 17 code cells)

| Cell | Type | Content |
|---:|---|---|
| 0 | md | Title, Group 5, requirements, constants, data source, headline results |
| 1 | md | Site selection — Kampot vs 9 other provinces |
| 2 | md | Weather dataset description, parameters, proxy note |
| 3 | code | Imports, paths, `LAT/LON`, `START = 2026-07-04` |
| 4 | code | `power_csv()` + `build_hourly()`; loads cached CSV or downloads actual 2026 + 2025 proxy |
| 5 | code | Dataset QC: rows, columns, source split, missing values, describe |
| 6 | md | GDD definition and formula |
| 7 | code | Daily aggregation, GDD/day, cumulative GDD, writes `Kampot_GDD_daily_2026.csv` |
| 8 | md | Soil/microclimate interpretation |
| 9 | code | Soil and gradient summary table |
| 10 | md | Stage-model logic |
| 11 | code | Reference thresholds from 2023–2025 |
| 12 | code | Predicted vs standard dates, deltas |
| 13 | code | 10-stage table, writes `Kampot_stage_predictions.csv` |
| 14 | md | Base-temperature robustness note |
| 15 | code | 8/10/12 °C sensitivity |
| 16 | md | Loss model and penalty table |
| 17 | code | Stressor counts and total loss |
| 18 | code | Proxy transparency breakdown |
| 19 | md | Daily-yield logic, area options |
| 20 | code | Bell curve, daily yield, writes `Kampot_daily_yield.csv` |
| 21 | code | ±50% coefficient sensitivity |
| 22 | code | Printed summary |
| 23 | md | Figures heading |
| 24 | code | GDD timeline figure |
| 25 | code | Daily yield figure |
| 26 | code | 2026 vs 2023–2025 GDD figure |
| 27 | md | Findings and limitations |
| 28 | md | Output-file table |

The notebook is **idempotent**: it reloads the cached CSVs when present; if the hourly CSV is missing, `build_hourly()` downloads from NASA POWER again. The reference CSV has the same cached-or-download behaviour.

---

## 8. Build and verification details

### Python environment
- Python **3.14** at `C:\Users\MK\AppData\Local\Programs\Python\Python314\python.exe`.
- Installed/used: `pandas`, `numpy`, `matplotlib`, `nbformat`, `nbconvert`, `openpyxl`, `pypdf`, `pymupdf` (`fitz`). `pdfplumber` is **not** installed.
- NBconvert emits a harmless warning about 3 images missing alt text; the HTML is fine.

### Rebuild scripts (outside the project, in temp)
`C:\Users\MK\AppData\Local\Temp\opencode\` contains:
- `build_notebook.py` — rebuilds the notebook from source.
- `execute_notebook.py` — executes all cells and scans for errors.
- `finalize_notebook.py` — final output cleanup/verification.
- Cold-start test artifacts.

If a rebuild is ever explicitly requested, the sequence used was:

```
python build_notebook.py
python execute_notebook.py
python finalize_notebook.py
python -m nbconvert --to html DSS301_Midterm_RedSky86_Kampot.ipynb
```

**Do not run this unless the user asks.** Rebuilding overwrites the frozen notebook, CSVs, figures, and HTML; if NASA has since released more actual 2026 data, results could shift and the group would have to resend. The current results are the submitted ones.

### Verification already performed
- Full execution: 0 errors; 29 cells; 17 code cells with outputs.
- Cold-start test: notebook rebuilt from raw NASA data and produced identical results (0.000 delta).
- Full audit of numbers, CSVs, figures, HTML.
- HTML regenerated after adding Group 5 names.

---

## 9. Rules and user preferences for future sessions

1. **Keep the teacher's files.** The user said: "keep my teacher files for now". Never delete `Midterm.jpg`, the PDFs, the XLSX files, the Khmer photos, or `Dataset Website.PNG`.
2. **Only `.py` leftovers were deleted.** `gdd_stage_model.py` and `yield_model.py` were removed because the notebook superseded them. Do not delete anything else without explicit permission.
3. **The deliverables are frozen.** The user does not want to resend files to the group ("i dont wanna resend to my group"). Only `Data_Download_Links.html` changed after the final notebook build; it is a helper page. If a change is unavoidable, say exactly which files changed and when, before making it.
4. **No AI/ML / "trending".** The analysis must stay a transparent, deterministic math pipeline. Every number must be traceable to the CSVs and formulas. Do not introduce a black-box model, an LLM step, or fitted-to-answer parameters.
5. **Real data only.** The weather is real NASA POWER/MERRA-2 data. The 2025 proxy is clearly labelled. Never call the dataset synthetic or imply that the 2025 rows are actual 2026 observations.
6. **Be precise with numbers and paths.** The user repeatedly asked for closure and exact file names. Give exact values, not vague summaries.
7. **Do not invent more work.** The project is done. If the user asks "are we done?", answer yes and point to the deliverables. The remaining items are questions for the teacher, not tasks.
8. **Group 5 naming:** Khon Sotheara · Khann Udomvirak · Chea Sela · Cheang Youpheng. The names are in the notebook title and the HTML export. No student IDs are included (none were provided).

---

## 10. Open items for the teacher (not unfinished work)

1. **Plantation area:** the teacher's materials show two possible interpretations — **113 blocks = 56.5 ha** (Zone A 66 + Zone B 47) or **69 planted plots = 34.5 ha**. Both totals are computed: **1,579.4 t** vs **964.4 t**. The correct area should be confirmed in class.
2. **Proxy acceptance:** 13/10/2026 → 02/12/2026 uses 2025 same-date weather because NASA POWER's 2026 data ended 12/10/2026. About 10.4 of the 27.86 loss points depend on this proxy. The teacher should confirm this is acceptable, or the notebook can be refreshed when NASA releases the remaining 2026 data.
3. **Coefficient assumptions:** the stress penalties (0.20%/h >35 °C, 0.02%/h RH ≥90%, etc.) are simplified assumptions for the exercise, not fitted field coefficients. The ±50% sensitivity table shows the result range. Be ready to justify them.
4. **End-of-cycle date nuance:** 150 DAT after 04/07/2026 is 01/12/2026, while the season/deliverable ends 02/12/2026 (DAT 151). The stage table shows 02/12 and +1 day. If the teacher asks, explain the 152-day inclusive season and the standard-calendar anchor. Do not silently change the submitted table.
5. **Student IDs:** only names are on the title; add IDs only if the teacher requires them.

---

## 11. Quick reference — key numbers

| Item | Value |
|---|---|
| Planting / 0 DAT | 04/07/2026 |
| End of harvest | 02/12/2026 (DAT 151) |
| Season length | 152 days inclusive |
| Base temperature | 10 °C |
| Season GDD | 2,685 |
| Mean GDD/day | 17.7 |
| Stage thresholds (GDD) | 146 / 562 / 836 / 1,105 / 1,370 / 2,673 |
| Stage accuracy | all 10 within ±1 day |
| Potential yield | 1.55 kg/plant |
| Weather loss | −27.86% (cap 35%) |
| Attained yield | 1.118 kg/plant |
| Yield per ha | 27,954 kg/ha |
| Harvest window | 17/09/2026 → 02/12/2026 (77 days) |
| Peak daily yield | 733.3 kg/ha/day on 25/10/2026 |
| Total, 56.5 ha (113 blocks) | 1,579.4 t |
| Total, 34.5 ha (69 plots) | 964.4 t |
| Plants, 56.5 ha | 1,412,500 |
| Plants, 34.5 ha | 862,500 |
| Actual weather | 04/07/2026 → 12/10/2026 |
| Proxy weather | 13/10/2026 → 02/12/2026 (2025 same dates) |
| Proxy share of loss | 10.38 of 27.86 points (~37%) |

---

## 12. Change log

- **2026-10-17** — notebook built, executed end-to-end, finalized; figures and CSVs written; HTML exported.
- **2026-10-17 09:11** — final rebuild after adding Group 5 names; notebook 343,382 bytes, HTML 706,966 bytes.
- **2026-10-18 11:22** — `Data_Download_Links.html` rewritten with the DAV deep link + 5 direct API links; all verified HTTP 200. This is the only file changed after the final build.
- **2026-11-18** — this `AGENTS.md` created from the old session export; the 3.2 MB `session-4f376d8d-2825-4747-9fb7-b3cebbb04769.md` dump was deleted.
- **2026-11-18** — project folder renamed `IDK` → `Chili Midterm`; workspace path updated in this file. Notebook/HTML contents untouched (frozen); their hard-coded `.../IDK` fallback path is now stale but harmless because the notebook resolves files from its own folder first.
- **2026-11-18** — new instructor files received (Week 4 lecture + worksheet, precision-agriculture deck, soil lesson + source chapters, soil lab report `DSSA.pdf`, QA/QC spreadsheets, Copernicus/SoilGrids/CamSIS links, and a Google Maps field pin).
- **2026-11-18** — full Pursat rebuild: NASA POWER hourly + 3 reference seasons downloaded for 12.1409 N / 104.0849 E, all CSVs and 4 figures written, notebook executed and exported to HTML. Kampot deliverables left frozen.
