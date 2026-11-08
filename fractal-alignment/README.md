# Fractal Alignment

A Pine Script v6 indicator for TradingView. It finds Fractal Model setups (C1 → C2 sweep and reclaim, confirmed by a lower-timeframe CISD) on a higher timeframe, draws them on the chart, and shows the higher-timeframe candles to the right of price. No library imports: everything runs inside the script.

## Install

Paste [fractal_alignment.pine](fractal_alignment.pine) into the Pine Editor, save, and add it to a chart.

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
- **Protected Swings:** continuations after the CISD. The swing must reach a point of interest (a fair value gap in the setup's direction, or an untaken swing it sweeps), then price closes through the opposite candles that led into it. A dot on the swing and a short line at the level; the dot turns grey once price takes the swing back.
- **Formation Liquidity:** the model's targets, the high (bullish) or low (bearish) of C1 and of the candle before it. A line ends at the bar that takes it.
- **SD projections** (off by default): multiples of the CISD ↔ C2 extreme range.
- **XC2:** price closes beyond the C2 extreme (red in C3, orange later; the model's SMT goes with it). **XC4:** C2 models only; during C4 price trades through C3's opposite extreme.

## HTF candles

- **Model HTF block** (1H on a 5m chart): 4–40 candles, size, offset. White / black candles.
- **HTF 1 / HTF 2** (off by default): extra blocks to the right. Auto = one and two steps above the model HTF, or pick 15m, 30m, 1H, 4H, 6H, 7H, D, W, M. They show Previous EQ, sweeps, SMT and PSP; no model runs on them. Up to 10 candles each.
- **HTF Label + Remaining Time** over and / or under each block, e.g. `1H (12:40)`.
- **HTF Open** and **Previous EQ** on the chart, on the HTF candles, or both. **L/H Lines** (off by default). **Time Labels** (off by default) under each candle.
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
- **PSP:** C1 and / or C2 close a different color than the pair's same candle (`Candle 1 / Candle 2 / Both`).

## Time Filter (off by default)

New setups only inside the checked windows (default New York 02:00–05:00, 08:00–11:00, 13:30–16:15), on charts below `Apply below` (1H).

## Info Table and ADR

```
   NQ1! (5m)
 5m-1H Model
   00:24:13
Bias: Neutral
SMT(Auto): ES1!, YM1!
```

One column; each row can be turned off. **ADR · CDR %** and the date are off by default.

- **ADR:** average high-low of the last 14 completed days (today excluded); pips on forex, points elsewhere. The day starts 18:00 New York (forex 17:00) on every broker.
- **CDR:** today's range so far as a share of ADR.
- **ADR+ / ADR- lines** (off by default): today's low + ADR, today's high − ADR.

## Alerts

**Alerts Settings** at the top: the master switch **Alerts?**, then **Model Candles?** (Candle 2 / 3 / 4 and CISD Confirmed), **Early CISD?**, **SMT Divergence?**, **Protected Swings?**. Set them up from the indicator's menu → Add alert. Separate conditions for the CISD alerts are in the alert dialog too.

## Other

- **Warnings** (off by default): a short message when the chart timeframe does not fit the fractal or a pair has no data.
- **Calculated bars** (2000, TradingView's own setting): the first fifth (at most 500) warms up the HTF candles; the model runs on the rest. Lower it for speed, raise it to see further back.

## Concepts

- **C1 / C2 / C3 / C4:** C1 is the reference candle, C2 the sweep / reclaim candle, C3 and C4 the follow-through. Bullish C2: low < C1 low and close > C1 low. Bearish C2: high > C1 high and close < C1 high.
- **CISD (Change in State of Delivery):** on the entry timeframe, a close through the open of the last run of opposite candles that led into the sweep.
- **Protected swing:** the extreme behind the CISD; the setup holds while price does not take it back.
- **Equilibrium (EQ):** the middle of a candle.
- **PSP (Precision Swing Point):** C1 or C2 closes a different color than the correlated pair's same candle.
- **SMT:** one of two correlated instruments takes the previous extreme and the other does not.

This is a charting tool, not trading advice.
