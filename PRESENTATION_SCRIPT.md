# Presentation script — 4 speakers

**Deck:** `DSS301_RedSky86_Pursat_Slides.pptx` (13 slides, 16:9)
**Total:** 13 minutes speaking + 5 minutes Q&A
**Rule of thumb:** never read the slide. The slide shows the number; you say what it means.

| Speaker | Slides | Section | Time |
|---|---|---|---|
| **Sotheara** | 1–4 | Problem, field, data, method | 3:30 |
| **Udomvirak** | 5–6 | GDD and growth stages | 2:45 |
| **Sela** | 7–8 | Yield loss and yield | 2:45 |
| **Youpheng** | 9–12 | Soil, cross-check, limits, actions | 3:30 |
| **All four** | 13 | Close | 0:30 |

Handovers are written into the script — say the last line, then step aside.

---

## Sotheara — slides 1–4 (3:30)

### Slide 1 · Title (0:25)
> "Good morning. We are Group 5, and we forecast growth stages and yield for a Red Sky 86 F1 chili field in Damnak Trayeung, in Phnum Kravanh district, Pursat. Udomvirak will take the heat accumulation, Sela the yield, and Youpheng the soil and our limitations."

### Slide 2 · The field (1:00)
> "One field, one season, three questions: when does each stage happen, how much yield does the weather take, and what does the soil add?
>
> The coordinates are not a district average — they are the exact pin our instructor gave us: 12.14 north, 104.08 east, on a NASA grid point at 121.9 metres elevation. Transplant is 4 July 2026 and the farm calendar ends 2 December.
>
> One thing matters for how you read this: every number is made for that one point. We are not describing the district, and we are not averaging several sites together."

### Slide 3 · Two sources (1:00)
> "We used two sources and nothing else.
>
> Weather is NASA POWER — MERRA-2 reanalysis, hourly, no API key. 3,648 rows, 15 parameters, and after our quality control, zero missing, zero duplicate and zero invalid records.
>
> Soil is the lab report our instructor supplied, Lab ID 11/23 from the RUPP laboratory, two sampling points, A1 and A2.
>
> We deliberately did not blend a second weather source. Averaging three reanalyses would give a number nobody can check, and our loss model is threshold-based, so the order of operations matters more than the ensemble. We did test a second source — ERA5-Land at nine kilometres — and it agrees on the thing that decides the yield, which you will see on slide 7."

### Slide 4 · Method (1:05)
> "Five steps, all in the notebook. Check every row first. Accumulate GDD at base 10 degrees from transplant. Calibrate the stage thresholds on three real seasons, 2023 to 2025, at the DAT dates in the agronomic standard. Apply six weather stress channels over the sensitive window — flowering to harvest — with total loss capped at 35 percent. Then distribute yield daily with a bell curve between first harvest and end of cycle.
>
> Nothing sits in our heads. Re-run the notebook and you get the same dates.
>
> Udomvirak will take you through what the heat actually did."

---

## Udomvirak — slides 5–6 (2:45)

### Slide 5 · GDD (1:20)
> "This is the whole season in one picture. The red line is daily maximum temperature, the blue line daily mean, and the orange line is cumulative GDD.
>
> The season reaches 2,621 GDD, which is 45 units below the three-season average — about 1.7 percent cooler. That sounds trivial, and for yield it nearly is. For timing it is not.
>
> Look at the shape: heat accumulates almost linearly through September, then the slope flattens in November as temperatures fall. That flattening is exactly why our last stage has a problem, which I will come to.
>
> For reference: 2023 gave 2,655, 2024 gave 2,681, and 2025 gave 2,662. Our 2026 season sits just under all three."

### Slide 6 · Stages (1:25)
> "Because we calibrated thresholds on those reference seasons rather than on a textbook, the predicted dates come out close: establishment one day late, vegetative one day late, flowering two days late, fruit set two days late, first harvest — 18 September — one day late.
>
> Two things to notice. First, the delay grows from one to two days as the season cools, which is exactly what accumulated heat predicts. Second, the model is not tuned to hit these dates; the offsets are outputs, and they stay inside plus or minus two days across all three reference seasons.
>
> The honest exception is the last row. End of cycle needs 2,649 GDD and we only reach 2,621, so that date is extrapolated at the late-season rate rather than observed. It is starred on the slide and flagged in the notebook — we would rather show a labelled extrapolation than quietly clip the season.
>
> Sela will show what that weather cost us."

---

## Sela — slides 7–8 (2:45)

### Slide 7 · Loss (1:25)
> "Six channels, and one of them does almost all the damage.
>
> Hours above 35 degrees: zero. Days above 50 millimetres of rain: zero. Dry spells beyond seven days: zero. Heat, heavy rain and drought did nothing this season.
>
> What did do damage is humidity. 1,209 hours with relative humidity at or above 90 percent during the sensitive window — that is a third of all hours — and at our disease-pressure coefficient that is 24.18 points of the 32.18 percent total loss.
>
> Warm nights and the 32-to-35 degree band add 8 points between them. Everything else is empty.
>
> So the story is not a hot season. It is a wet, humid one, and the risk is fungal and bacterial disease, not heat stress.
>
> Two caveats. Thirty-three percent of that loss rests on the labelled 2025 proxy tail where NASA data has not arrived yet. And when we cross-checked against ERA5-Land at nine kilometres, it also found zero hours above 35 degrees — the heat channel is robust across both sources, even though ERA5-Land runs wetter on humidity."

### Slide 8 · Yield (1:20)
> "Apply that loss to the variety potential of 1.55 kilos per plant and we get 1.051 kilos, which at 25,000 plants per hectare is 26,280 kilos per hectare — 26.3 tonnes.
>
> The curve peaks at 680 kilos per hectare per day on 26 October, and the harvest window runs 77 days from 18 September to 4 December. Total for the 56.5 hectare block option is 1,484 tonnes; for the 34.5 hectare option, 907 tonnes.
>
> One caution we will repeat at the end: 26 tonnes per hectare is at the top of the commercial range for chili. It is defensible for a fertigated hybrid at this density, but it is a model output, not a measured harvest.
>
> Youpheng will take the soil and what we would actually do with this."

---

## Youpheng — slides 9–12 (3:30)

### Slide 9 · Soil (1:00)
> "The lab report covers two fields. Both are silt loam — around 20 percent clay and 60 percent silt — which is good for chili: good drainage, decent water holding.
>
> The problem is pH. 7.16 and 7.11 against a chili optimum of 5.0 to 6.5. Both fields sit about 0.6 units too alkaline, which locks up micronutrients — iron, zinc, manganese — even though the soil is otherwise fertile.
>
> Available phosphorus is very high: 155 and 173 parts per million. That is not a soil you add phosphate to.
>
> A2 is the better field: base saturation 78 percent against 65, and nearly three times the potassium. Plant-available water is about 90 millimetres, which is why the dry-spell channel stayed empty."

### Slide 10 · Sensitivity (1:00)
> "A forecast is only as good as its assumptions, so we pushed on the ones that move the answer. Halve every loss coefficient, or double every one, and attainable yield stays between 1.01 and 1.30 kilograms per plant — a 26 percent spread, not a factor of two.
>
> The timing is tighter still. Re-run the same thresholds against 2023, 2024 and 2025 and the stages land within about two days of where the standard calendar puts them.
>
> So the two conclusions we care about — harvest starts on 18 September, and humidity costs us the yield — do not depend on any single coefficient we chose."

### Slide 11 · Limits (0:50)
> "What we did not measure. A third of the season is a labelled proxy. One date is extrapolated. Five inputs — potential yield, density, the loss coefficients, the planted area and soil water — are assumptions carried from the method, not measurements from this field. And the weather grid is 55 kilometres, which is coarse for one field near the Cardamoms.
>
> We list these because a marker can check any of them, and because the next step is obvious: a rain gauge in the field and the real planted area."

### Slide 12 · Actions (0:40)
> "Four things we would tell the farm. Disease first — 1,209 humid hours means scouting and preventive spraying, not heat protection. Skip phosphate, the soil has plenty. Fix pH slowly over seasons, not in one application. And plant A2 first, it is the stronger field.
>
> The aim is to protect the 26 tonnes we modelled, not to chase a bigger potential."

---

## All four — slide 13 (0:30)

**Sotheara:**
> "That is our analysis. The notebook, the data and the figures are on GitHub — the link is on the slide. Thank you, we are happy to take questions."

*(All four stand at the front for Q&A. Whoever owns the slide answers; Sotheara fields anything about method.)*

---

## Q&A bank — prepared answers

**"Why not average several weather sources?"**
> "Because our loss model is threshold-based. Hours above 35 degrees and hours above 90 percent humidity do not average linearly, so an averaged dataset produces a loss that matches no real record and cannot be reproduced. One primary source plus a documented cross-check is defensible; a blend is not."

**"How can you predict a season that has not finished?"**
> "We do not. Everything up to 12 October is observed. After that we use the same calendar dates from 2025, labelled as a proxy in the data and in the text, and it accounts for a third of the modelled loss. The stage dates are driven by the observed part, which is why they stay within two days."

**"Is 26 tonnes per hectare realistic?"**
> "It is at the top of the commercial range. It comes from 1.55 kilos per plant at 25,000 plants per hectare, after a 32 percent weather loss. For a fertigated hybrid under good management it is achievable; we present it as a model output with that caveat, not as a measured harvest."

**"Did you check your data?"**
> "Yes — 3,648 hourly rows, zero missing, zero duplicate, zero invalid rain values, and the daily aggregates are rebuilt from those checks. The notebook prints the completeness flags."

**"Why is the end date extrapolated?"**
> "The threshold is 2,649 GDD and the season reaches 2,621. Rather than clip the curve, we extended it at the last-14-day accumulation rate and flagged the row as extrapolated. It is a two-day extension, not a guess about yield."

**"What would change your conclusion most?"**
> "A local rain gauge. Rain and humidity are the two channels that drive the loss, and a 55-kilometre grid cannot resolve a single field's rainfall. That is the first thing we would add."

---

## Delivery notes

- **Numbers:** say them slowly and only once. "Twenty-six thousand two hundred and eighty kilos per hectare" — then move on.
- **Timing:** if you run long, cut slide 4 detail and slide 10 explanation, never the limitations slide.
- **Backup:** keep the executed notebook open in a tab. If asked "show me", the output is already rendered.
- **Wording:** say "model output" and "assumption" before someone asks. Owning the gaps is what earns the marks.
