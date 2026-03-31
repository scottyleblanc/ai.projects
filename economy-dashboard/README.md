# Economic Indicators — Mortgage Decision Aid

A single-file HTML dashboard that pulls live Canadian economic data and translates it into plain-English signals to help with personal financial decisions — specifically whether to lock in a fixed mortgage rate or stay on a variable rate.

No server required. Open the HTML file in any browser.

---

## What it does

The dashboard monitors eight key economic indicators arranged in two rows — four market/price indicators across the top, four macro/rate indicators across the bottom. Each card shows a current value, day-over-day change, 30-day sparkline where applicable, and a plain-English signal.

An **Overall read** panel weighs all eight signals together and gives a single paragraph recommendation. A **How to read this** panel below it explains what each signal means for your mortgage decision.

There is also a weekly observation log (saved in browser localStorage) so you can note what you're seeing over time.

---

## Layout

```
Row 1 (market/price):  [ S&P 500 ]  [ TSX Canada ]  [ Crude Oil ]  [ CAD/USD ]
Row 2 (macro/rate):    [ BoC Rate ] [ Inflation    ] [ GoC 5yr   ] [ GoC 10yr ]
```

---

## The eight indicators

### Why these eight?

The goal is to filter out media noise and focus on a small number of signals that have a direct, documented relationship to Canadian mortgage rates. Each one measures the same underlying question — how confident are businesses, consumers, and investors in the future? — from a different angle.

---

### 1. S&P 500 (via SPY ETF)

**What it is:** The S&P 500 is a broad index of 500 large US companies. SPY is the most liquid ETF that tracks it.

**Why it matters for mortgages:** Equity markets are a real-time vote on economic confidence. A sustained decline (–10% or more over a month) signals that institutional investors are pricing in a recession. Recessions cause central banks to cut rates — which favours staying variable. Rising markets suggest the economy is healthy and rates may stay elevated.

**What to watch for:**
- Falling sharply (< –10% over month) → recession risk rising, rate cuts may follow, may favour variable
- Declining moderately (–3% to –10%) → softening confidence, watch for BoC commentary
- Rising strongly (> +5%) → rates likely to stay elevated

**Data source:** Twelve Data free API (SPY quote + daily time series). Requires free API key.
**Note:** SPY is priced in USD per share. The percentage move is what matters, not the absolute number.

---

### 2. TSX Canada (via EWC ETF)

**What it is:** EWC (iShares MSCI Canada ETF) is a US-listed ETF that holds the same large-cap Canadian companies that make up the TSX Composite. It is priced in USD on NYSE Arca.

**Why it matters for mortgages:** The TSX more directly reflects the Canadian economy than the S&P 500 — it is heavily weighted toward financials (~30%), energy (~17%), and materials (~13%), the three sectors most sensitive to Canadian rate decisions. A falling TSX signals weakening domestic confidence and increases the likelihood of a BoC rate cut.

**Why EWC instead of the TSX index directly:** The S&P/TSX Composite index (GSPTSE) and TSX-listed ETFs such as XIU.TO are not available on the Twelve Data free plan — the free tier covers US-listed instruments and forex pairs only. EWC holds the same underlying companies and has a correlation of >0.95 with the TSX Composite, making it a reliable directional proxy. The only difference is that it is priced in USD.

**What to watch for:**
- Falling sharply (< –10% over month) → Canadian recession signals, rate cuts may follow
- Declining moderately (–3% to –10%) → softening Canadian confidence, watch for BoC response
- Rising strongly (> +5%) → strong Canadian confidence, rates likely to stay elevated

**Data source:** Twelve Data free API (EWC quote + daily time series). Requires free API key.

---

### 3. Crude Oil (via USO ETF)

**What it is:** USO (United States Oil Fund) is an ETF that tracks WTI crude oil prices. WTI (West Texas Intermediate) is the primary North American oil benchmark, priced in USD.

**Why it matters for mortgages:** Oil is both an input cost and a leading inflation indicator. When oil prices spike, inflation rises across the economy. When inflation rises, the BoC is less likely to cut rates — or may raise them. As a Canadian, oil prices also directly affect the CAD and government revenues, making them especially relevant to the BoC's decision-making.

**Why USO instead of WTI/USD directly:** Twelve Data's free tier provides the WTI/USD current quote but locks the historical time series behind a paid plan. USO tracks WTI closely and is available in full on the free tier. The directional signal is the same.

**What to watch for:**
- Spiking (> +15% over month) → inflation pressure building, BoC less likely to cut
- Rising moderately (+5% to +15%) → watch for BoC commentary
- Falling sharply (< –10%) → reduced inflation pressure, supports potential rate cuts

**Data source:** Twelve Data free API (USO quote + daily time series). Requires free API key.

---

### 4. CAD/USD Exchange Rate

**What it is:** How many US dollars one Canadian dollar buys.

**Why it matters for mortgages:** A weakening CAD signals that foreign capital is leaving Canada — often a sign of economic stress or declining confidence in the Canadian economy. A sharp drop in the CAD can put upward pressure on interest rates, as the BoC may need to respond to the underlying economic weakness driving the decline. A strengthening CAD suggests foreign investors are confident in Canada's economic outlook.

**What to watch for:**
- CAD weakening (< –3% over month) → economic stress signal, watch for BoC response
- CAD stable → no significant stress signal

**Data source:** Twelve Data free API (CAD/USD forex quote + daily time series). Requires free API key.

---

### 5. Bank of Canada Overnight Rate

**What it is:** The rate at which major banks lend money to each other overnight. The BoC adjusts this on eight fixed dates per year.

**Why it matters for mortgages:** Variable mortgage rates are priced directly off this rate. When the BoC raises it, your variable payment goes up almost immediately. When it cuts, your payment drops.

The card also displays the **next scheduled BoC announcement date** and how many days away it is. The 2026 schedule is hardcoded from the BoC's published annual calendar (Jan 28, Mar 18, Apr 29, Jun 10, Jul 15, Sep 9, Oct 28, Dec 9).

**What to watch for:**
- Rising → strong case to lock in fixed immediately
- Falling → stay variable and ride the cuts down
- Held → no immediate change, watch the other indicators for direction

**Data source:** Bank of Canada Valet API (`V39079`). No key required — CORS-enabled.
**Update frequency:** 8 times per year on fixed announcement dates. The date shown is the last decision date, which may be weeks ago — this is correct, not stale.

---

### 6. Inflation — CPI

**What it is:** Statistics Canada's All-items Consumer Price Index for Canada, published monthly. The card shows year-over-year percentage change (the headline number the BoC targets) and month-over-month change.

The sparkline plots the **year-over-year inflation rate** (not the raw index level) over the last 13 months. This shows whether the pace of inflation is rising or falling — which is what the BoC responds to. A raw CPI index level rises almost every month regardless of whether inflation is cooling, so it would be misleading as a sparkline. Green = inflation rate falling; red = inflation rate rising.

**Why it matters for mortgages:** The BoC's inflation target is 2% year-over-year. When CPI is well above 2%, the BoC is unlikely to cut and may raise rates — which favours locking in fixed. When CPI is falling toward or below 2%, the BoC has room to cut — which may favour staying variable.

**What to watch for:**
- CPI > 3% YoY → well above target, BoC unlikely to cut, may raise — consider locking
- CPI 2.5–3% YoY → above target, limits BoC room to cut
- CPI 1.5–2% YoY → near or at target, BoC has flexibility
- CPI < 1.5% YoY → below target, supports further rate cuts

**Data source:** Statistics Canada Web Data Service (WDS) API, vector `v41690973` (All-items CPI, not seasonally adjusted, table 18-10-0004-01). No key required — CORS-enabled.
**Update frequency:** Monthly, released approximately the third week of the following month.

---

### 7. GoC 5-Year Bond Yield

**What it is:** The yield (effective interest rate) on a 5-year Government of Canada bond.

**Why it matters for mortgages:** Fixed mortgage rates in Canada are priced off the 5-year GoC bond yield, plus a spread of roughly 1–2% set by lenders. When the 5-year yield rises, fixed mortgage rates follow within weeks. This gives you advance warning before your bank changes its posted rates.

**What to watch for:**
- Rising strongly (> +0.30% over month) → fixed rates about to rise, lock soon
- Rising moderately (+0.10% to +0.30%) → fixed rates may rise, watch
- Falling moderately (–0.10% to –0.30%) → fixed rates may ease
- Falling strongly (< –0.30% over month) → fixed rates improving, no rush to lock

**Data source:** Bank of Canada Valet API (`BD.CDN.5YR.DQ.YLD`). No key required.
**Update frequency:** Each business day. On weekends, the date shown will be the most recent Friday — this is correct.

---

### 8. GoC 10-Year Bond Yield

**What it is:** The yield on a 10-year Government of Canada bond. This card also shows the **10yr–5yr spread** — the difference between the two yields.

**Why it matters for mortgages:** The 10-year yield reflects long-term growth and inflation expectations. More importantly, the spread between the 10-year and 5-year yields is a leading economic indicator:

- **Positive spread (normal):** 10yr > 5yr — investors expect the economy to grow.
- **Negative spread (inverted):** 10yr < 5yr — investors expect economic trouble ahead, pricing in future rate cuts. An inverted yield curve has historically preceded every major Canadian and US recession.

**What to watch for:**
- Inverted spread (10yr < 5yr) → recession signal, BoC rate cuts likely ahead, may favour variable
- 10yr rising sharply → long-term inflation expectations building
- 10yr falling sharply → long-term confidence weakening, easing pressure ahead

**Data source:** Bank of Canada Valet API (`BD.CDN.10YR.DQ.YLD`). No key required.
**Update frequency:** Each business day. If either bond yield date is more than 5 calendar days old, the dashboard flags it with a warning.

---

## Reading the signals together

The dashboard is most useful when multiple indicators point in the same direction. One signal alone is noise; three pointing the same way is meaningful.

| Scenario | What to consider |
|---|---|
| BoC rate rising + 5yr bond rising | Strong case to lock in fixed immediately |
| BoC rate falling + equities declining | Rate cuts likely ahead, staying variable may save money |
| CPI above 3% + oil spiking | Inflation pressure on two fronts — BoC unlikely to cut |
| CPI falling + yield curve inverted | Disinflation + recession signal — rate cuts likely ahead |
| Oil spiking + CAD weakening | Inflationary pressure + economic stress — complex, watch closely |
| All signals neutral | Use this as time to assess your personal risk tolerance, not a trigger to act |

**Important:** These signals inform context. Your mortgage decision also depends on personal factors — how much payment variability you can absorb, how long you plan to stay in the home, and the spread between current fixed and variable rates at your lender. A mortgage broker can help weigh those personal factors alongside the economic environment.

---

## Signal quick reference

```
BoC rate RISING          → Variable payments up immediately. Lock in fixed urgently.
BoC rate FALLING         → Variable payments falling. Stay variable, ride cuts down.
5yr bond RISING          → Fixed rates will rise in weeks. Lock before they do.
5yr bond FALLING         → Fixed rates improving. No urgency to lock.
10yr < 5yr (inverted)    → Recession signal. Rate cuts likely. May favour variable.
CPI above 3%             → Inflation too high. BoC unlikely to cut. Consider locking.
CPI below 1.5%           → Inflation cooling. BoC may cut further. May favour variable.
S&P / TSX falling hard   → Recession risk. BoC likely to cut. May favour variable.
Oil spiking              → Inflation risk. Rates may stay higher longer.
CAD weakening            → Economic stress signal. Watch for BoC response.
```

---

## Setup and usage

### Requirements

- Any modern browser (Chrome, Firefox, Safari, Edge)
- A free Twelve Data API key (for S&P 500, TSX/EWC, crude oil, CAD/USD)
- No server, no hosting, no installation

### Getting your Twelve Data API key

1. Go to [twelvedata.com/register](https://twelvedata.com/register)
2. Create a free account — no credit card required
3. Copy your API key from the dashboard

**Free tier limits:** 800 API calls per day, 8 calls per minute. The dashboard uses 8 calls per refresh (quote + time series × 4 symbols). At weekly use, you will never approach the daily limit.

### First-time setup

1. Download `economy-dashboard.html`
2. Open it in any browser — the API key prompt appears automatically
3. Paste your Twelve Data key and click "Save & load data"
4. The key is saved in your browser's localStorage — you only do this once per browser

### Updating your key

Click the **⚙ API key** button in the top-right header at any time.

### Refresh behaviour

After each refresh, a 90-second cooldown timer appears. This prevents accidentally burning through the per-minute rate limit by rapid refreshing.

The four government API calls (BoC rate, GoC bond yields, Statistics Canada CPI) all fire in parallel at the start — they have no Twelve Data quota and load immediately. The four Twelve Data symbols (SPY, EWC, USO, CAD/USD) are then sequenced with 8-second gaps between each pair, keeping the burst rate within the 8 calls/minute free tier ceiling.

A full refresh takes approximately 32 seconds.

---

## Hosting on Cloudflare Pages (optional — access from any device)

The dashboard can be hosted as a static site for free on Cloudflare Pages, making it accessible from any device at a permanent URL with no server to maintain.

**First-time deployment:**

1. Go to [pages.cloudflare.com](https://pages.cloudflare.com) and create a free account
2. Click **Create a project → Direct Upload**
3. Name your project (e.g. `economy-dashboard`) — this becomes part of your URL
4. Rename `economy-dashboard.html` to `index.html`
5. Put `index.html` into a zip file
6. Drag the zip onto the Cloudflare upload area and click **Deploy**
7. Your dashboard is live at `https://economy-dashboard.pages.dev`

The file must be named `index.html` — Cloudflare won't serve a non-index file at the root URL automatically. The zip wrapper is required by Cloudflare's direct upload for site deployments.

**Updating after changes:**

1. Download the updated `economy-dashboard.html`
2. Rename it to `index.html`
3. Zip it
4. Go to your Cloudflare Pages project → **Deployments → Upload** and upload the new zip

The URL stays the same across all devices. Takes about two minutes.

**API key across devices:**

Each new browser or device will prompt for your Twelve Data API key on first load. Paste it once and it is saved in that browser's localStorage — you won't be asked again on that device. This is intentional: the key is never embedded in the file itself, so it is safe to host the dashboard at a public URL.

**Note on GitHub OAuth:** If Cloudflare prompts you to connect GitHub, click **Direct Upload** instead — you do not need a GitHub connection. If you do connect GitHub, select **Only select repositories** on the OAuth screen to limit access to a single repo rather than your entire account.

---

## Data sources

| Indicator | Source | API | Symbol / Vector | Key required |
|---|---|---|---|---|
| BoC Overnight Rate | Bank of Canada | Valet API | `V39079` | No |
| GoC 5yr Bond Yield | Bank of Canada | Valet API | `BD.CDN.5YR.DQ.YLD` | No |
| GoC 10yr Bond Yield | Bank of Canada | Valet API | `BD.CDN.10YR.DQ.YLD` | No |
| Inflation (CPI) | Statistics Canada | WDS API | `v41690973` | No |
| S&P 500 | Twelve Data | SPY ETF | quote + time series | Yes (free) |
| TSX Canada | Twelve Data | EWC ETF | quote + time series | Yes (free) |
| Crude Oil | Twelve Data | USO ETF | quote + time series | Yes (free) |
| CAD/USD | Twelve Data | CAD/USD forex | quote + time series | Yes (free) |

**Bank of Canada Valet API:** [bankofcanada.ca/valet/docs](https://www.bankofcanada.ca/valet/docs) — free, no registration, CORS-enabled.

**Statistics Canada WDS API:** [statcan.gc.ca/en/developers/wds](https://www.statcan.gc.ca/en/developers/wds) — free, no registration, CORS-enabled.

**Twelve Data:** [twelvedata.com](https://twelvedata.com) — free tier, registration required, 99.95% uptime SLA. Upgrade to Grow ($29/month) to remove daily call limits — no code changes required.

---

## Technical notes

### Architecture

The dashboard is a single self-contained HTML file with no external dependencies beyond Google Fonts and the two APIs. All state is held in memory during the session. Notes are persisted to browser localStorage (up to 24 entries, most recent 6 displayed).

### Why not AWS / hosted infrastructure?

The current version is a deliberate single-file design — no server, no build step, no infrastructure to maintain. For weekly personal use this is the right tradeoff.

**Planned migration to AWS:** A future version will move to an AWS-backed architecture. Running data fetching server-side removes all of the browser-side constraints that currently shape the design:

- API keys live on the server — no per-device key entry, no risk of keys being visible in browser storage
- No CORS restrictions — any data source is accessible server-to-server, including Nasdaq Data Link for GoC bond yields
- No Twelve Data rate-limit sequencing — the server fetches on a schedule, the browser gets one fast cached response
- The 90-second cooldown timer and all API call sequencing logic can be removed from the dashboard entirely
- The GoC bond yield feed issue (BoC Valet API benchmark transition gaps) becomes solvable — Nasdaq Data Link or another server-accessible source can be used instead

The planned architecture is a Lambda function behind API Gateway that fetches and caches all 8 indicators, with the dashboard calling a single `/api/data` endpoint rather than 8 external APIs directly.

### Why ETFs instead of direct index or commodity data?

Twelve Data's free plan covers US-listed stocks, ETFs, and forex pairs. Index symbols (GSPTSE, WTI/USD) and non-US-listed securities (XIU.TO) return 404 on the free tier. The ETF proxies used are:

| Target | Proxy ETF | Rationale |
|---|---|---|
| S&P 500 index | SPY | Direct tracker, most liquid US ETF |
| TSX Composite | EWC | US-listed, holds same Canadian large-caps, correlation >0.95 |
| WTI Crude Oil | USO | Tracks WTI closely, free-tier accessible |

### Rate limit handling

The dashboard detects all Twelve Data error codes:
- `401 / 403` → prompts to re-enter the API key
- `429` → shows "Rate limited — wait ~60 seconds then refresh" in amber on affected cards
- Other errors → shows "Could not load — refresh to retry" in grey

### Sparkline reference

Each card's sparkline uses a consistent approach: it plots the raw value (price, yield, or rate) over time, and the line color reflects whether that value is higher or lower at the right edge than at the left edge. Green means the trend is favourable for variable-rate holders; red means it is unfavourable.

The one exception is **Inflation (CPI)**, where the sparkline plots the year-over-year inflation rate rather than the raw index level. This is intentional — the CPI index level rises almost every month regardless of whether inflation is cooling, which would make the sparkline permanently red and therefore meaningless. Plotting the YoY rate instead shows the direction that actually matters for BoC decisions.

| Card | Sparkline plots | Green means | Red means |
|---|---|---|---|
| S&P 500 | Daily SPY closing price, 30 trading days | Price up vs 30 days ago | Price down vs 30 days ago |
| TSX Canada | Daily EWC closing price, 30 trading days | Price up vs 30 days ago | Price down vs 30 days ago |
| Crude Oil | Daily USO closing price, 30 trading days | Price up vs 30 days ago | Price down vs 30 days ago |
| CAD/USD | Daily CAD/USD rate, 30 trading days | CAD stronger vs 30 days ago | CAD weaker vs 30 days ago |
| BoC Rate | No sparkline | — | — |
| Inflation (CPI) | YoY inflation rate (%), 13 months | Inflation rate falling | Inflation rate rising |
| GoC 5yr Yield | Daily yield, 30 business days | Yield lower (fixed rates easing) | Yield higher (fixed rates rising) |
| GoC 10yr Yield | Daily yield, 30 business days | Yield lower vs 30 days ago | Yield higher vs 30 days ago |

### Why do the BoC and CPI dates look old?

**Overnight rate** (`Last decision: YYYY-MM-DD`): The BoC only changes or confirms rates on 8 fixed dates per year. The date shown is the last decision date. If the rate was last changed in March and it is now May, the date will be from March. This is correct.

**Bond yields** (`Latest business day: YYYY-MM-DD`): Published each business day. On weekends the date will be Friday; on public holidays, the previous trading day. Dates more than 5 calendar days old trigger a warning flag reading **"BoC feed delayed — last known value"**.

**Known issue — benchmark bond transitions:** The BoC periodically switches to a new benchmark bond issue. When this happens, the `BD.CDN.5YR.DQ.YLD` and `BD.CDN.10YR.DQ.YLD` series in the Valet API can stop publishing new values for several weeks until the new issue begins being recorded under the same series name. During this period the dashboard displays the last known values with the stale warning. The data is still directionally useful as a reference point. The feed typically recovers on its own within 4–6 weeks of a benchmark transition — no dashboard changes are required when it does.

**CPI** (`YYYY-MM`): Published monthly, approximately three weeks after the reference month ends. The date shown is the reference month, not the release date — it will always appear to lag by 4–6 weeks. This is correct.

The Statistics Canada WDS API does not guarantee the sort order of observations in its response. The dashboard explicitly sorts all returned observations by `refPer` (reference period) descending before indexing, so `obs[0]` is always the most recent month regardless of API response order. 25 periods are requested (rather than 14) to ensure enough data points are available to compute 13 months of year-over-year rates for the sparkline.

---

## Files in this project

| File | Description |
|---|---|
| `economy-dashboard.html` | The complete dashboard — open this in a browser |
| `README.md` | This file |

---

## Planned and potential improvements

### In progress

- **AWS migration:** Move data fetching server-side (Lambda + API Gateway). Resolves the BoC bond yield feed issue, removes per-device API key entry, eliminates rate-limit sequencing, and enables any data source regardless of CORS policy. See the AWS architecture note in Technical notes above.

### Future

- **GoC bond yield alternative source:** Once server-side, switch bond yields from the BoC Valet API (subject to benchmark transition gaps) to Nasdaq Data Link or a similar reliable source.
- **Historical snapshots:** Store weekly indicator values to enable 3-month or 6-month trend charts (AWS Lambda + DynamoDB).
- **Push notifications:** Email or text alert when a threshold is crossed (e.g., 5yr bond yield rises more than 0.5% in a week).
- **Mortgage rate feed:** Pull actual posted 5-year fixed rates from a Canadian mortgage aggregator to show alongside the bond yield that drives them.
- **Upgrade to paid Twelve Data:** Unlocks GSPTSE and WTI/USD directly, removing the need for ETF proxies.

---

## Disclaimer

This dashboard is an informational tool for personal use. It is not financial advice. Mortgage decisions depend on personal circumstances that no dashboard can capture — payment tolerance, term length, lender spreads, and personal risk tolerance. Consult a mortgage broker or financial advisor before making rate decisions.
