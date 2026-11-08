# Fractal Alignment

A Pine Script v6 indicator for TradingView. It finds Fractal Model setups (C1 → C2 sweep and reclaim, confirmed by a lower-timeframe CISD) on a higher timeframe, draws them on the chart, and shows the higher-timeframe candles to the right of price. No library imports: everything runs inside the script.

## Install

Paste [fractal_alignment.pine](fractal_alignment.pine) into the Pine Editor, save, and add it to a chart.

## Settings at a glance

In the order they appear in the settings window. Defaults work out of the box; details in the sections below.

| Group | What it does | Default |
|---|---|---|
| Alerts Settings | Master switch + model, Early CISD, SMT and protected swing alerts | On (Early CISD off) |
| Warnings | Message when the timeframe or a pair does not fit | Off |
| General Settings | History, fractal (Automatic / manual / Custom), C2 / C3 / C4 models, bias (Neutral, Bullish, Bearish, Auto Bias 1 / 2), Hide Setups | History 1, Automatic, Neutral |
| HTF Candles | Model HTF block (count, size, offset, colors), HTF 1 / HTF 2 extra blocks, HTF Open, Previous EQ, L/H lines | 4 candles, Small, extra blocks off |
| HTF Candles Time Labels | Time under each candle, block title (timeframe + time left), 12h / 24h, time zone | On, title above and below |
| Model Style | CISD, Early CISD, candle EQ, C1 sweep, labels, T-Spot | All on (Early CISD off) |
| Protected Swings | Dots and lines after the CISD | On |
| Asset Correlation | SMT and PSP, pairs (Automatic / Custom), labels, line style | On, Automatic |
| Standard Deviation Projections | Levels from the CISD ↔ C2 extreme range | Off |
| Formation Liquidity | C1 targets until taken | On |
| Time Filter | New setups only inside checked windows | Off |
| ADR / CDR | ADR length, ADR± lines | 14 days, lines off |
| Info Table | Rows, style (Text / Table), size, position | Text, bottom right |

## Model

The indicator picks a **model HTF** from the chart timeframe (`Fractal: Automatic`):

| Chart | Model HTF |
|---|---|
| 1s / 15s | 1m / 5m |
| 1m | 15m |
| 3m | 30m |
| 5m | 1H |
| 15m / 30m | 4H |
| 1H | Daily |
| 4H | Weekly |
| Daily | Monthly |

Manual choices: `1s-1m, 15s-5m, 1m-15m, 3m-30m, 5m-1H, 15m-4H, 1H-1D, 4H-1W, 1D-1M` and `Custom`. The `C2 / C3 / C4` boxes choose the model types:

- **C2 model:** C2 takes C1's high / low and closes back inside.
- **C3 model:** C2 does not close back inside; C3 closes beyond C2's body without taking C2's extreme.
- **C4:** the continuation (its T-Spot).

A model is drawn only once its **CISD** on the entry timeframe confirms. **History** sets how many earlier setups stay drawn next to the latest one (0 = only the latest). **Bias:** Neutral, Bullish, Bearish, or **Auto Bias 1 / 2** (only setups in the direction of the model one / two fractals higher while it runs; on 5m-1H: AB1 = 15m-4H, AB2 = 1H-1D).

On the chart:

- **C2 / C3 label** at the swing; `C2+PSP` when the candle colors diverge from a correlated pair.
- **T-Spot (C3 / C4):** between the previous HTF candle's equilibrium (EQ) and the current candle's open, growing bar by bar, with the EQ as a dotted line. `C4` at the start of the C4 box.
- **CISD line:** ends one candle after the close through the level. **Early C2 CISD** (off by default): a CISD before C2 closes, dotted.
- **Candle 1 Sweep:** from the C1 extreme that C2 took to the end of C2.
- **Protected Swings:** continuations after the CISD. The swing must reach a point of interest (a fair value gap in the setup's direction, or an untaken swing it sweeps), then price closes through the opposite candles that led into it. A dot on the swing and a short line at the level; the dot turns gray once price takes the swing back.
- **Formation Liquidity:** the model's targets, the high (bullish) or low (bearish) of C1 and of the candle before it. A line ends at the bar that takes it.
- **SD projections** (off by default): multiples of the CISD ↔ C2 extreme range.
- **XC2:** price closes beyond the C2 extreme (red in C3, orange later; the model's SMT goes with it). **XC4:** C2 models only; during C4 price trades through C3's opposite extreme.

## HTF candles

- **Model HTF block** (1H on a 5m chart): 4–40 candles, size (Small / Normal / Large), offset. White / black candles, packed close together.
- **HTF 1 / HTF 2** (off by default): extra blocks to the right. Auto = one and two steps above the model HTF (15m, 1H, 4H, D, W, M), or pick 15m, 30m, 1H, 90m, 4H, 6H, 7H, D, W, M; Auto never picks 90m, 6H or 7H. They show Previous EQ, sweeps, SMT and PSP; no model runs on them. Up to 10 candles each.
- **HTF Label + Remaining Time** above and below each block by default (`Top` / `Bottom` to keep one): the timeframe in bold, the time left in brackets under it, e.g. `1H` / `(12:40)`.
- **HTF Open** and **Previous EQ** on the chart, on the HTF candles, or both. **L/H Lines** (off by default). **Time Labels** under each candle (on by default): open time, the day name on daily, the week's Monday date on weekly (`NOV 2`), the month on monthly.
- **Live sweep:** when the forming candle takes the previous candle's extreme and trades back inside.
- **SMT** between consecutive HTF candles, including the forming one; hidden once a newer candle trades beyond it.
- **Hide Setups?** hides every model drawing and model alert; the HTF candles stay.

## Asset Correlation (SMT + PSP)

- Two pairs, automatic, from the chart's own broker:
  - Futures: NQ ↔ ES + YM, ES ↔ NQ + YM, YM / RTY ↔ ES + NQ, GC ↔ XAUEUR + XAUGBP, SI ↔ XAGEUR + XAGGBP, CL ↔ RB + HO, HO ↔ CL + RB, RB ↔ CL + HO, BTC ↔ ETH (micros too). A dated contract (NQZ2026) pairs with the same contract (ESZ2026).
  - CFD / forex: US100 ↔ US500 + US30, US500 ↔ US100 + US30, US30 ↔ US500 + US100, XAUUSD ↔ XAUEUR + XAUGBP, XAGUSD ↔ XAGEUR + XAGGBP, EURUSD ↔ GBPUSD, AUDUSD ↔ NZDUSD, USDJPY / USDCHF / USDCAD, USOIL ↔ GASOLINEF + HEATINGOILF.
  - Crypto: BTCUSDT ↔ ETHUSDT (also `.P` perpetuals and BTCUSD ↔ ETHUSD).
  - `Custom`: two symbols by hand, each with `Inverse?` (e.g. DXY).
- **SMT on the chart** belongs to a setup: **reversal** from the C1 extreme to the C2 extreme, and **continuation** after C2 closes (a pair goes beyond its own C2 extreme while price does not; the line's end follows price). The label names the pair.
- **SMT color:** bullish SMT in the Bullish CISD color (blue), bearish in the Bearish CISD color (red), on the chart and on every HTF block. Pick a color for `SMT Line` to draw every SMT in that one color.
- **PSP:** C1 and / or C2 close a different color than the pair's same candle (`Candle 1 / Candle 2 / Both`).

## Time Filter (off by default)

New setups only when the model is found inside a checked window (when C3 opens, or C4 for a C3 model), on charts below `Apply below` (1H). Three windows, New York time unless a UTC offset is set: 02:00–05:00, 08:00–11:00, 13:30–16:15, all unchecked by default (no window checked = nothing filtered).

## Info Table and ADR

```
   NQ1! (5m)
 5m-1H Model
   00:24:13
Bias: Neutral
SMT (Auto): ES1!, YM1!
  ADR 245.5 · 62%
```

One column; each row can be turned off. **ADR · CDR %** is on by default, the date off. Style `Text` (default) is text only with no background; `Table` adds a background. `Border?` adds a frame.

- **ADR:** average high-low of the last 14 completed days (today excluded); pips on forex, points elsewhere. The day starts 18:00 New York (forex 17:00) on every broker.
- **CDR:** today's range so far as a share of ADR.
- **ADR+ / ADR- lines** (off by default): today's low + ADR, today's high − ADR.

## Alerts

**Alerts Settings** at the top: the master switch **Alerts?**, then **Model Candles?** (Candle 2 / 3 / 4 and CISD Confirmed), **Early CISD?**, **SMT Divergence?**, **Protected Swings?**. To create one: the indicator's menu (three dots next to its name) → Add alert, condition **Any alert() function call**. An alert keeps the settings it was created with; after changing them, create it again. The dialog also lists separate conditions for the CISD alerts (Bullish / Bearish CISD Confirmed, Bullish / Bearish Early CISD).

## Other

- **Warnings** (off by default): a short message when the chart timeframe does not fit the fractal or a pair has no data.
- **Calculated bars** (2000, TradingView's own setting): the first fifth (at most 500) warms up the HTF candles; the model runs on the rest. Lower it for speed, raise it to see further back.

## Colors and dark theme

One soft gray palette (TradingView's own grays) with blue / red for bullish / bearish. On a dark chart background, colors left at their default (bear body, borders, wicks, C1 sweep, block titles, C labels, table background) switch to light tones automatically; a color you pick yourself is kept.

## Concepts

- **C1 / C2 / C3 / C4:** C1 is the reference candle, C2 the sweep / reclaim candle, C3 and C4 the follow-through. Bullish C2: low < C1 low and close > C1 low. Bearish C2: high > C1 high and close < C1 high.
- **CISD (Change in State of Delivery):** on the entry timeframe, a close through the open of the last run of opposite candles that led into the sweep.
- **Protected swing:** the extreme behind the CISD; the setup holds while price does not take it back.
- **Equilibrium (EQ):** the middle of a candle.
- **PSP (Precision Swing Point):** C1 or C2 closes a different color than the correlated pair's same candle.
- **SMT:** one of two correlated instruments takes the previous extreme and the other does not.

This is a charting tool, not trading advice.
