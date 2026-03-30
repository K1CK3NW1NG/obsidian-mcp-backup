# Deep-Dive: Polymarket Trading Bots + Moon Dev

**Vault:** [[../../ME]] | [[../../WORK]] | Related: [[../../02 - Projects/Gain More Leads]]

**Date:** 2026-03-25
**Previous sessions:** None

---

## Quick Summary
- Moon Dev (@moondevonyt) teaches Polymarket trading bots through his paid platform algotradecamp.com — two verified strategies: spread market-maker and mean-reversion predictor
- Polymarket runs on Polygon (ETH L2) and uses a Central Limit Order Book (CLOB) — Python SDK `py-clob-client` is the standard tool for bot development
- Auth flow: Ethereum private key (L1) → derive API credentials (L2) → place orders in USDC
- The best open-source LLM-powered Polymarket bot (dylanpersonguy) uses a 3-model ensemble (GPT-4o + Claude + Gemini) with 15-point risk checks, Fractional Kelly sizing, and whale tracking — this is the production reference architecture
- Claude Code is the right environment to build this: Python bot + Anthropic API for LLM decisions + py-clob-client for execution

---

## Key Findings

### Who Moon Dev Is
- Creator and operator of algotradecamp.com — an algorithmic trading education platform
- GitHub: @moondevonyt — 2.4k followers; repos: Harvard-Algorithmic-Trading-with-AI (265 stars), Moon-Dev-Code (177 stars)
- Philosophy: "code is the great equalizer" — open sources educational code on YouTube but core strategy code is paywalled
- Methodology: **RBI Framework** — Research → Backtest → Implement — his repeatable process for any market or strategy
- Active interest in AI agent frameworks (forked: elizaOS, agent-zero, ZerePy, goose) beyond basic bots
- Primary exchange integrations: Hyperliquid (derivatives), ccxt (multi-exchange), Polymarket (prediction markets)
- Uses Cursor (AI IDE) for development workflow

### What Moon Dev Builds on Polymarket
- Teaches 3 Polymarket strategies inside algotradecamp.com's Prediction Bot Course:
  1. **Spread market-maker** — places limit orders on both sides of the order book, earns the bid-ask spread as trades fill
  2. **Mean-reversion predictor** — bets on market prices returning to historical baseline; enters when price deviates significantly from expected probability
  3. Third strategy undisclosed publicly
- Also teaches Solana sniper bots and copy-trading bots that mirror whale trades
- His bots are Python-based; he integrates LLMs primarily for research/decision-making rather than just execution

### How Polymarket Works (Technical)
- **Platform:** Polygon blockchain (L2), USDC.e for settlement, POL for gas
- **Token model:** Each market creates Yes + No ERC1155 tokens; each resolves to $1 USDC at settlement
- **Three APIs:**
  - Gamma API (`gamma-api.polymarket.com`) — market discovery, search, events; no auth required
  - Data API (`data-api.polymarket.com`) — positions, trades, analytics; no auth required
  - CLOB API (`clob.polymarket.com`) — order book, pricing, order management; auth required for trading
- **Order types:** Limit (GTC or FOK), Market (buy by dollar amount)
- **Critical:** Only markets with `enableOrderBook: true` are CLOB-tradeable — filter before attempting orders
- **WebSocket:** Real-time order book and price feeds; bots should subscribe rather than poll for performance
- **Heartbeat:** Bots must send periodic pings to keep limit orders alive — orders auto-cancel without heartbeat

### Authentication Flow (Step-by-Step)
1. Create Ethereum wallet (MetaMask or generate private key)
2. Fund with USDC.e and POL on Polygon
3. Set token allowances for USDC and conditional tokens at the CTF Exchange contracts (EOA only; email wallets skip)
4. Install `py-clob-client` via pip
5. Initialize `ClobClient(host, key=PRIVATE_KEY, chain_id=POLYGON, signature_type=0)`
6. Call `create_or_derive_api_creds()` once to get L2 API credentials
7. Use L2 credentials for all subsequent trading calls

### Trading Bot Strategies (Ranked by Complexity)
| Strategy | Complexity | Edge Source | Moon Dev Teaches? |
|---|---|---|---|
| Copy trading | Low | Follow top traders | Via Solana bots (similar pattern) |
| Spread market-making | Medium | Bid-ask spread | Yes — Prediction Bot Course |
| Mean-reversion | Medium | Historical probability drift | Yes — Prediction Bot Course |
| Directional AI research | High | LLM probability vs. market price | Implied by his agent work |
| Ensemble multi-model AI | Very High | Multi-LLM consensus + calibration | Community (dylanpersonguy) |

### LLM-in-the-Loop Architecture (How AI Bots Decide)
Based on analysis of the best open-source Polymarket AI bot:
1. **Market Discovery** — Gamma API scans active markets with filters (volume, spread, time to resolution)
2. **Pre-Research Filter** — LLM or rules screen out low-quality markets (saves ~90% of research costs)
3. **Evidence Gathering** — Web search per market category (e.g., "site:bls.gov" for economic data)
4. **Multi-Model Forecast** — 2-3 LLMs each estimate probability independently
5. **Calibration** — Platt scaling adjusts raw LLM probabilities toward historical accuracy
6. **Edge Calculation** — `edge = model_probability - market_price`
7. **Risk Check** — 15 independent checks: drawdown, spread, liquidity, kill switch, etc.
8. **Position Sizing** — Fractional Kelly: `f = (edge / (1 - market_price)) * kelly_fraction`
9. **Execution** — Simple for small orders; TWAP/Iceberg for larger positions

### Risk Management (Non-Negotiable Rules)
- **Kelly criterion** — Never bet more than Kelly-optimal fraction of bankroll
- **Drawdown heat system** — Reduce position sizing automatically as losses accumulate (4 levels)
- **Hard caps** — Max bet per market, max daily loss, max open positions
- **Heartbeat loop** — Keep-alive pings to prevent order auto-cancellation
- **Paper trading first** — All serious bots default to paper mode and require explicit flag to go live
- **Kill switch** — Single flag that halts all trading immediately

### Key Risk Factors Specific to Polymarket
- **Resolution risk** — A market can resolve against you even if your probability estimate is correct
- **Liquidity risk** — Thin markets have wide spreads; large orders move price against you
- **Sports market timing** — Orders auto-cancel when games start; need to manage exposure timing
- **Smart contract risk** — Funds are on-chain; wallet security is critical

### Community Bot Ecosystem
- **probablyprofit** — Natural language strategy framework: write edge as a sentence, LLM handles execution
- **polymarket-copy-trading-bot** — Copy top traders via Supabase real-time subscriptions
- **Polymarket-BTC-15-Minute-Trading-Bot** — Signal fusion (sentiment + spike detection + divergence) for BTC markets; uses NautilusTrader + Redis + Grafana
- **polymarket-kalshi-weather-bot** — 31-member ensemble weather forecasting across Polymarket + Kalshi
- **Fully-Autonomous-Polymarket-AI-Trading-Bot** — Production-grade 3-LLM ensemble with whale tracking, 15-point risk checks, Flask dashboard — best open-source reference

### Fee Structure & Maker Rebates
- **Geopolitics:** 0% fees — completely free, best for directional AI bets (keep 100% of profits)
- **Sports:** ~0.44% effective max fee — low, fast resolution (same-day games), highest daily volume
- **Finance:** new fee rate from March 30 — **50% maker rebate** back daily (highest rebate on platform)
- **Politics / Economics / Weather / Tech:** new rate from March 30 — 25% maker rebate back daily
- **Crypto:** ~1.56% effective max fee — highest fees, avoid for market-making
- Maker rebate mechanism: Polymarket redistributes 20–50% of all taker fees daily in USDC to market makers — makes spread bots significantly more profitable than headline fee rates suggest
- SDK clients (Python/TS/Rust) handle fee calculation automatically; REST API users must fetch `/fee-rate` and include `feeRateBps` manually

### Best Markets for Bot Trading (Fastest Turnover + Lowest Fees)
1. **Same-day NBA / NFL / Soccer** — resolve same night, 0.44% max fee, $1M–$4M daily volume per game, 25% maker rebate
2. **Daily weather markets** — resolve every 24 hours, low fees, consistent volume, 25% maker rebate
3. **Geopolitics (directional only)** — 0% fees, but weeks/months to resolve — best for AI directional bets, not market-making
4. **Finance markets** — 50% maker rebate makes these the highest-yield for spread bots despite new fee introduction
5. **Short crypto windows (4-hour BTC/ETH)** — same-day resolution but highest fees (1.56%) — use only with strong edge

### Leaderboard Intelligence (Top Polymarket Traders)
- Top earner: +$4M/month, $12M volume — soccer specialist (EPL, Champions League, PSG)
- #2 earner: +$3.76M/month — Arsenal and Everton EPL markets
- Highest volume trader: $206M volume (almost certainly algorithmic/bot) — strong signal that market-making at scale is viable
- Pattern: top 5 traders all concentrate on soccer/football markets — EPL and Champions League have the best pricing inefficiencies
- Copy-trading these wallets via the Data API leaderboard endpoint is a viable low-effort strategy

### Resolution Timeline
- Undisputed: ~2 hours after proposal → funds redeemable
- Disputed (1 challenge): 4–6 days
- Disputed (2 challenges, escalated to UMA vote): ~48 additional hours
- Bots should avoid markets with ambiguous resolution criteria to prevent disputes locking capital

---

## What Your References Covered
- Moon Dev's X profile (@moondevonyt) — X auth wall blocked content retrieval; confirmed via cross-referencing his GitHub and algotradecamp that his content themes are: Python trading bots, AI agents, algorithmic trading education, Polymarket prediction markets

---

## What the Broad Search Added
- Full Polymarket API architecture (3 APIs, auth flow, heartbeat, token allowances)
- Moon Dev's actual Polymarket strategies (spread market-maker, mean-reversion) from algotradecamp
- His RBI methodology framework as the underlying process for all his bot work
- Production reference architecture for LLM-driven Polymarket bots (dylanpersonguy)
- Community bot ecosystem with 5 distinct approaches
- Critical technical gotchas: token allowance setup, `enableOrderBook` filtering, heartbeat requirement
- Position sizing using Fractional Kelly criterion with drawdown heat system

---

## Actionable Takeaways
- Install `py-clob-client` and get credentials set up first — this is the foundation every Polymarket bot needs
- Filter markets with `enableOrderBook: true` via Gamma API before attempting any orders
- Set token allowances for USDC and conditional tokens before first trade — silent failure without this
- Implement heartbeat loop or limit orders will auto-cancel — critical for market-maker bots
- Start in paper trading mode; require explicit environment variable to enable live trading
- Apply Kelly criterion for position sizing — never bet more than Kelly-optimal fraction
- Moon Dev's simplest strategy (spread market-maker) is the lowest-risk entry point: provide liquidity, earn spread, no directional research required
- For AI-driven bots: use Claude as the forecaster, calibrate its probabilities against historical accuracy, and set minimum edge threshold (e.g., 15%+) before trading

---

## Ideas & Things to Build

### Starter Bot (Week 1)
- Spread market-maker on high-volume Polymarket markets
- Stack: Python + py-clob-client + Gamma API for market discovery
- Logic: place buy order at midpoint - N ticks, sell order at midpoint + N ticks; cancel and reprice every minute
- Add heartbeat loop and basic drawdown kill switch

### AI Research Bot (Week 2-3)
- Market scanner that finds markets where model probability deviates from market price by 15%+
- Use Claude (Anthropic API) to research each market: provide context, news, base rates → get probability estimate
- Apply Fractional Kelly for position sizing
- Paper trade for 2 weeks, track Brier scores before going live

### Copy-Whale Bot
- Use Data API leaderboard to identify top 10 Polymarket traders by ROI
- Monitor their positions via WebSocket
- Replicate their trades at 1-5% scale with proportional sizing
- Supabase or SQLite for position tracking

### Full Production Bot (Month 2)
- Multi-market scanner + evidence gathering (web search per market category)
- 2-model ensemble: Claude + GPT-4o with Brier-score-based weighting
- 15-point risk check before every order
- Drawdown heat system (4 levels, auto-reduces sizing)
- Discord/Telegram alerts for high-conviction signals
- Equity curve + P&L dashboard

### Moon Dev Style Integration
- Apply his RBI framework to Polymarket:
  - Research: backtest probability estimation accuracy for specific market categories
  - Backtest: simulate trades against Polymarket historical data via Data API
  - Implement: deploy with paper trading → live with Kelly sizing

---

## Source Index
- [github.md](./github.md)
- [web.md](./web.md)
- [twitter.md](./twitter.md)
