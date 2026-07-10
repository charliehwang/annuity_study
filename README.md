# Annuity Scenario Explorer

A single-file web app for exploring how a fixed index annuity with an income rider behaves under different assumptions. No installation, no internet required — double-click `annuity-scenario-explorer.html` and it opens in your browser.

## Why the charts didn't show the first time

The original version loaded the charting library (Chart.js) from an internet CDN. If the file is opened offline, or in a preview window that blocks external scripts, the charts silently fail to render. **Fixed:** Chart.js v4.4.1 is now embedded directly inside the HTML file, so it works anywhere with zero network access.

## The product being modeled

The annuity has two separate values:

| | Cash value | Protected Income Value (PIV) |
|---|---|---|
| What it is | Real money you can surrender/withdraw | A bookkeeping number used only to calculate income |
| Growth | Slow (modeled here as a flat annual rate, default 3%) | 25% day-one boost, then a step-up every 5 years |
| Can you cash it out? | Yes | **No — never** |

### PIV math (as described in your notes)

```
Day-one PIV      = Premium × (1 + boost)                    e.g. 500K × 1.25 = 625K
Step-up per 5yr  = 5yr index return × multiplier            e.g. 32% × 2.5 = 80%
PIV after N steps = Day-one PIV × (1 + step-up)^N           e.g. 625K × 1.8³ = 3,645K
Annual income    = PIV at income age × withdrawal rate      e.g. 3,645K × 4.3% = 156.7K
```

Only **completed** 5-year periods count. Start at 50, income at 63 → 2 step-ups, and years 60–63 add nothing to the PIV. (Verify this against the actual contract — some products credit interim interest.)

## Using the app

- **Sliders (left panel):** premium, start age, income age, day-one boost, 5-year index return, PIV multiplier, withdrawal rate, estimated cash-value growth, and a comparison return for investing the premium directly.
- **Presets (top):** one click sets the index-return assumption to the advisor's pitch (32%/5yr ≈ 5.7%/yr), a moderate 20%, or a weak-markets 10%.
- **Pin scenario:** freezes the current PIV curve and income as a gray ghost so you can change assumptions and compare two scenarios side by side.
- **Charts:** the main chart shows PIV (blue steps), cash value (gray dashed), and a direct-investment line (teal). The bar chart compares annual income: annuity (PIV × withdrawal rate) vs. direct investment using the 4% rule.
- **Step-up table:** the exact schedule of every 5-year credit, so you can trace each number.

## Default scenario (matches your notes)

$500K premium at age 50, income at 65, 25% boost, 32% index return per 5 years, 2.5× multiplier, 4.3% withdrawal → **PIV $3.645M, income $156,735/yr**.

## What to watch out for

- **The PIV is not your money.** Surrender the contract and you get the cash value, which is far smaller. The gap between the blue and gray lines is the cost of leaving.
- **The 80% step-up is an assumption, not a guarantee.** It needs the index to return 32% every 5 years *and* the 2.5× multiplier to hold. Carriers can typically reset participation/multiplier rates at each renewal. Try the 20% and 10% presets to see the sensitivity: income drops from ~$157K to ~$95K and ~$52K respectively.
- **Cash value growth is an estimate.** Your notes said "grows slowly" without a formula; the 3%/yr default is a placeholder — adjust the slider to match the actual illustration.
- **Fees, riders, caps, and spreads are not modeled.** Real contracts often have income-rider fees (~1%/yr) deducted from cash value, and caps or spreads on index credits.

## Verification performed

The app was tested headlessly in a simulated browser (jsdom + canvas):

- Both charts instantiate and render (line chart: 3 datasets × 16 points; bar chart renders income comparison).
- Default output matches the hand calculation exactly: PIV $3.645M, income $156,735/yr.
- Sliders recompute live (index return 10% → income $52K ✓).
- Presets and the pin/compare feature work (pinned ghost dataset appears ✓).
- Zero external network references remain in the file.

*This tool illustrates contract mechanics under user-supplied assumptions. It is not financial advice — verify all mechanics against the actual policy illustration and contract.*
