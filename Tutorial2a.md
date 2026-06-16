# Lab 2a – Total Cost of Ownership (TCO)

## What is TCO?

Total Cost of Ownership (TCO) is the full cost of buying and running something over its entire useful life — not just the sticker price. It includes every expense you'll incur: consumables, power, maintenance, support, and eventual replacement.

**Why it matters in IT:** Almost every major IT decision — cloud vs on-premises servers, outsourcing vs in-house support, which laptop vendor to standardise on — gets evaluated primarily through TCO analysis. A cheaper upfront cost often becomes the more expensive option over 3–5 years.

---

## The Task

Compare **two printers** over a 5-year period using these standard assumptions:

| Assumption | Value |
|---|---|
| Period | 5 years |
| Pages printed per week | 750 pages |
| Printer powered on per week | 40 hours |
| Weeks per year | 52 |
| **Total pages over 5 years** | **195,000 pages** |
| **Total hours powered on over 5 years** | **10,400 hours** |

---

## Step 1 – Choose your two printers

A good comparison is a **budget inkjet** vs a **laser printer**, since they have very different cost structures. For example:

- **Printer A:** Canon PIXMA TR4660 (~$69 AUD) — budget home inkjet
- **Printer B:** HP LaserJet Pro M404n (~$399 AUD) — entry-level office laser

Search Harvey Norman, Officeworks, or JB Hi-Fi for current prices and model specs. Note the exact model and price you find.

---

## Step 2 – Identify all cost items

### Suggested cost categories:

**Fixed costs (one-off purchase)**
- Printer purchase price
- USB cable (if not included)
- Installation/setup time (value your time at some hourly rate)

**Variable costs (repeat purchases over 5 years)**
- Ink or toner cartridges
- Paper (A4 ream)
- Replacement drum unit (laser only — separate from toner)
- Maintenance kit (laser printers need periodic roller/fuser replacement)

**Ongoing costs**
- Electricity consumption
- Warranty/extended support (optional)

---

## Step 3 – Research unit costs

### Example research (use your own current prices):

| Item | Inkjet (PIXMA TR4660) | Laser (LaserJet M404n) |
|---|---|---|
| Printer price | $69 | $399 |
| Black ink cartridge | $29 (approx 400 pages) | — |
| Colour ink cartridge set | $45 (approx 300 pages) | — |
| Black toner cartridge | — | $85 (approx 3,000 pages) |
| Drum unit | — | $120 (approx 30,000 pages) |
| A4 paper (500 sheets) | $8 | $8 |
| Power consumption | 13W printing / 1.5W idle | 8.5W printing / 4.5W idle |
| Electricity cost | ~$0.30/kWh (check your state) | ~$0.30/kWh |

---

## Step 4 – Calculate consumption

Work out how many of each consumable you'll need over 5 years (195,000 pages total).

### Example calculation (Inkjet):

**Paper:**
195,000 pages ÷ 500 sheets per ream = **390 reams**
390 × $8 = **$3,120**

**Black ink cartridges** (assume 70% of pages are black-only):
195,000 × 0.70 = 136,500 black pages
136,500 ÷ 400 pages per cartridge = **341 cartridges**
341 × $29 = **$9,889**

**Colour ink cartridges** (assume 30% of pages use colour):
195,000 × 0.30 = 58,500 colour pages
58,500 ÷ 300 pages per set = **195 sets**
195 × $45 = **$8,775**

**Electricity (inkjet):**
Assume 750 pages/week at ~1 min per page = 12.5 hours printing per week
Power: 13W × 12.5h = 162.5 Wh printing + 1.5W × 27.5h idle = 41.25 Wh idle
Weekly: ~0.204 kWh × 52 weeks × 5 years = **53 kWh total**
53 × $0.30 = **$15.90**

### Example calculation (Laser):

**Toner cartridges:**
195,000 pages ÷ 3,000 pages per cartridge = **65 cartridges**
65 × $85 = **$5,525**

**Drum unit:**
195,000 pages ÷ 30,000 pages per drum = **6.5 → round up to 7 drums**
7 × $120 = **$840**

**Paper:** Same as above = **$3,120**

**Electricity (laser):**
12.5h printing × 8.5W = 106.25 Wh + 27.5h × 4.5W idle = 123.75 Wh
Weekly: ~0.23 kWh × 52 weeks × 5 years = **59.8 kWh**
59.8 × $0.30 = **$17.94**

---

## Step 5 – Total it up

| Cost Item | Inkjet | Laser |
|---|---|---|
| Printer purchase | $69 | $399 |
| Paper (5 years) | $3,120 | $3,120 |
| Ink/Toner (5 years) | $18,664 | $5,525 |
| Drum unit | — | $840 |
| Electricity | $16 | $18 |
| **TOTAL TCO** | **~$21,869** | **~$9,902** |

---

## Step 6 – Conclusion

**1. Based on the TCO, which printer is the "best"?**
The laser printer has a significantly lower 5-year TCO (~$9,902 vs ~$21,869) despite costing more upfront, because the per-page cost of laser toner is much lower than inkjet ink. For high-volume printing the laser is clearly better value.

**2. Would your answer be the same for a home user who prints 5 pages per week?**
No. At 5 pages per week (1,300 pages over 5 years), ink cartridge consumption drops dramatically. The inkjet's low purchase price dominates the TCO at low volumes, and the laser's toner cost advantage doesn't have enough pages to show through. The inkjet wins at low volumes.

**3. What other factors could/should we have considered apart from TCO?**
- Print quality (photos vs text documents)
- Print speed (pages per minute)
- Reliability and mean time between failures
- Environmental impact (ink waste, toner cartridge recycling)
- Physical size/footprint
- Noise level
- Vendor support and availability of consumables locally

**4. What would you look for in a large workgroup printer?**
Knowing that consumable costs dominate TCO, you'd prioritise: high-yield toner cartridges (lower cost per page), a high-capacity drum/fuser, low power consumption in standby, and a long manufacturer-rated page life (duty cycle). Purchase price matters much less than ongoing cost per page at high volume.

**5. At what point do both printers cost the same?**
Find the "break-even" point by setting up a formula in your spreadsheet:
`$69 + (pages × inkjet_cost_per_page) = $399 + (pages × laser_cost_per_page)`

Inkjet cost per page ≈ $18,664 / 195,000 = ~$0.096/page
Laser cost per page ≈ $6,365 / 195,000 = ~$0.033/page

Break-even: ($399 - $69) / ($0.096 - $0.033) = 330 / 0.063 ≈ **~5,238 pages**

At roughly 5,238 pages printed (about 7 weeks at 750 pages/week), the laser becomes the cheaper option overall.

---

## Spreadsheet layout (suggested)

Set up your LibreOffice Calc spreadsheet with columns like:

| Cost Item | Unit Cost | Qty (5yr) | Total |
|---|---|---|---|
| Printer purchase | $69 | 1 | $69 |
| Paper | $8/ream | 390 | $3,120 |
| Black ink | $29 | 341 | $9,889 |
| ... | | | |
| **TOTAL** | | | **=SUM(D2:D10)** |
