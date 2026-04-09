# ✈ Airline Booking UX Friction Analyzer

**Live dashboard:** [botondboros.github.io/airline-ux-friction](https://botondboros.github.io/airline-ux-friction)

A data-driven analysis of online booking flows across 16 European airlines, measuring the friction a user experiences when purchasing a one-way ticket from Budapest (BUD) to London.

---

## What is the Friction Index?

The Friction Index starts at 0 and accumulates as the user moves through the booking flow. Every friction event adds points. Every positive UX signal reduces them. **Lower is better** — think of it as a golf score.

A score of 0 represents a perfectly frictionless booking experience. There is no theoretical upper bound.

```
Friction Index = 0 (start)
+ LOGIN_PUSH          +7   Login wall before search results
+ LATE_FEE            +7   Mandatory fee revealed only at payment
+ PRE_TICKED          +6   Paid option auto-selected (insurance, seat, boarding)
+ BURIED_BASIC        +6   Cheapest fare visually suppressed
+ PRICE_CHANGE        +5   Total price changes between steps
+ HIDDEN_DECLINE      +4   Decline/skip option small or absent
+ CONFIRMSHAMING      +4   Guilt-inducing copy on decline button
+ ACCOUNT_PUSH        +4   Loyalty/account banner mid-flow
+ NO_SKIP_SEAT        +3   No clear seat selection skip
+ LOYALTY_PUSH        +3   Loyalty programme pushed on passenger form
+ SLOW_PAGE           +3   Page load exceeding 4 seconds
+ COOKIE_POPUP        +2   Cookie consent banner
+ UPSELL_SCREEN       +1   Optional ancillary product screen
+ FORM_FIELD_EXTRA    +1   Each required field beyond baseline 5
- GUEST_CHECKOUT      -2   Guest checkout clearly available
- CLEAR_SKIP          -2   Skip option clearly labelled
- PRICE_TRANSPARENT   -2   No surprise fees at payment
```

---

## Airlines Analyzed

| Airline | Type | Route |
|---|---|---|
| Ryanair | Ultra-LCC | BUD → STN |
| Wizz Air | LCC | BUD → LTN |
| easyJet | LCC | BUD → LGW |
| Norwegian | LCC | BUD → LGW |
| British Airways | FSC | BUD → LHR |
| Lufthansa | FSC | BUD → LHR |
| KLM | FSC | BUD → LHR |
| SAS | FSC | BUD → LHR |
| TAP Air Portugal | FSC | BUD → LHR |
| Icelandair | FSC | BUD → LHR |
| ITA Airways | FSC | BUD → LHR |
| Aegean Airlines | FSC | BUD → LHR |
| Austrian Airlines | FSC | BUD → LHR |
| Finnair | FSC | BUD → LHR |
| Air France | FSC | BUD → LHR |
| Swiss | FSC | BUD → LHR |

**Test parameters:** 1 adult · one-way · cheapest available fare · ~3 weeks from run date

---

## Grading Scale

| Grade | Friction Index | Label |
|---|---|---|
| A+ | 0–5 | Excellent |
| A | 6–10 | Very Good |
| B | 11–16 | Good |
| C | 17–22 | Moderate |
| D | 23–32 | Fair |
| F | 33+ | Needs Improvement |

---

## Methodology

### Data Collection
Each booking flow is automated using **Playwright** (headful Chromium, 1440×900 viewport, slowMo 250ms). The scraper navigates from the airline homepage through to the payment page, logging every friction event in real time.

At each step the `StepLogger` records:
- Screenshots
- Click count
- Form fields encountered
- Price changes (via DOM text pattern matching)
- Popups and overlays
- Pre-ticked checkboxes (via `querySelectorAll('input:checked')`)
- Page load times

### AI Analysis Layer
Screenshots are analyzed by **Claude Vision API**, which independently assesses:
- UI complexity
- Price visibility and consistency
- CTA clarity
- Dark pattern detection (confirmshaming copy, buried options, ambiguous skip affordances)

### Objective Metrics (per airline)
- 🖱️ Total clicks from search to payment
- 📋 Required form fields
- 💰 Number of price changes across the flow
- 🚧 Mandatory account creation (yes/no)
- ⏱️ Average page load time
- 🍪 Cookie/popup interruptions
- 💺 Upsell screens (seat, bag, insurance)

### Limitations
- Airline websites change frequently — selectors may become outdated
- Some carriers (Ryanair, Wizz Air) use aggressive bot detection that can interfere with automated flows
- The scraper follows the cheapest available fare path; premium fare flows may differ
- Results reflect the state of each website at the time of the scrape
- Airlines marked **DEMO** use researched estimates, not live measurements

---

## Tech Stack

- **Playwright** — browser automation and screenshot capture
- **Claude API (claude-opus-4-5)** — Vision analysis of booking flow screenshots
- **Node.js** — scraper orchestration and dashboard generation
- **Vanilla HTML/CSS/JS** — self-contained single-file dashboard (no framework dependencies)

---

## Running Locally

```bash
# Clone and install
git clone https://github.com/botondboros/airline-ux-friction
cd airline-ux-friction
npm install
npx playwright install chromium

# Set API key
set ANTHROPIC_API_KEY=your_key_here   # Windows
export ANTHROPIC_API_KEY=your_key     # Mac/Linux

# Run a single airline
node scraper/runner.js --airline klm

# Run multiple
node scraper/runner.js --airline klm,norwegian,finnair

# Rebuild dashboard
node dashboard/buildDashboard.js
```

Output files:
```
output/
  screenshots/<airline>/   step-by-step screenshots
  logs/<airline>.json      raw friction event log
  dashboard.html           open in browser
```

---

## Project Structure

```
├── scraper/
│   ├── runner.js              orchestrator
│   ├── stepLogger.js          friction event recorder
│   └── airlines/              one .js file per airline
├── dashboard/
│   ├── buildDashboard.js      HTML generator
│   ├── data.js                friction flows & demo data
│   └── mockups.js             SVG friction point illustrations
├── config/
│   └── airlines.js            airline metadata & URLs
└── output/
    └── dashboard.html         generated report
```

---

## Author

Botond Boros — Head of BI & Digital Solutions, Hungarian Tourism Agency (MTÜ)

[LinkedIn](https://www.linkedin.com/in/botondboros) · [GitHub](https://github.com/botondboros)
