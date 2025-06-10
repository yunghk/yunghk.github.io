---
title: "How to Analyze Brazilian REITs with Python"
layout: post
date: 2025-06-10
excerpt_separator: <!--more-->
---

Brazilian Real Estate Investment Funds, or *Fundos Imobiliários (FIIs)*, have become a popular option for income-seeking investors. But not all REITs are created equal—and when it comes to filtering through dozens of them, intuition is rarely enough.

This post walks through how I built a fully functional REIT screener using Python. From pulling raw data to ranking based on key metrics, I explain every step and decision so you can replicate—and adapt—it for your own strategy.

<!--more-->

## Why Build a REIT Analyzer?

When I started investing in FIIs, I relied on blog posts, YouTube videos, and occasional forum recommendations. But those rarely agreed with each other—and even when they did, I couldn’t tell if it was due to real performance or simply copycat narratives.

I wanted a tool I could trust. One that:

- Pulled live data directly from Funds Explorer (one of Brazil’s best FII data sources)
- Applied filters transparently
- Ranked REITs based on my rules, not someone else’s marketing

This wasn’t just about saving time. It was about investing with conviction backed by data.

---

## Step 1 – Environment Setup

Before anything, let’s set the technical base. I used:

- **Python 3.10+**
- `pandas` for data wrangling
- `requests` and `json` to access and parse APIs
- `matplotlib` for visualizations

```bash
pip install pandas requests yfinance matplotlib
```

You don’t need a fancy IDE. I used JupyterLab so I could document reasoning inline.

---

## Step 2 – Fetching Raw Data from Funds Explorer

Funds Explorer doesn’t have a public API—but it exposes structured data through JSON endpoints. The most important ones:

- `/get-ranking`: Main listing of all FIIs
- `/dividends-by-period`: Dividend history per ticker

I built a helper function to robustly fetch and parse:

```python
def get_parsed_json(url, params, headers):
    try:
        r = requests.get(url, headers=headers, params=params)
        data = json.loads(r.text)
        return data if not isinstance(data, str) else json.loads(data)
    except Exception as e:
        logging.error(f"Error fetching from {url}: {e}")
        return None
```

This acts as our ingestion engine. Once we fetch everything into a `DataFrame`, we move to analysis.

---

## Step 3 – First Filters: Sanity Checks

We start with basic filters:

- Dividend Yield (DY) > 0.7% monthly
- P/VP ratio between 0.8 and 1.2
- Market Cap above R$100M
- Trading volume not null

Why these?

Because I want REITs that (a) pay regularly, (b) aren’t overpriced, and (c) are liquid enough to trade. This filters out the fringe.

We can preview our filtered universe like this:

```python
filtered_df[['ticker', 'dy', 'p_vp']].sort_values(by='dy', ascending=False).head(10)
```

> 📊 **Figure 1** – Top 10 filtered REITs by Dividend Yield

---

## Step 4 – Add Sector Insight

REITs aren't all the same. Paper (CRI) funds behave differently from logistics or shopping centers. So I mapped tickers to sectors using a dictionary.

This helps in portfolio construction: overexposure to a sector like offices (especially post-pandemic) may skew your risk.

> 🧭 **Insight** – Categorizing funds isn't always clear-cut. Some are "hybrid" and require qualitative judgment.

---

## Step 5 – Dividend Consistency

High yield doesn’t mean much if it's not regular. I looped over each ticker to query its dividend history. Any fund with fewer than 11 monthly payouts in the last year was excluded.

```python
for ticker in selected_df['ticker']:
    divs = get_parsed_json(dividends_url, {'ticker': ticker}, headers)
    # analyze for 12-month consistency
```

> 📈 **Figure 2** – Monthly dividend pattern for selected REITs

---

## Step 6 – Normalize & Score

We now have three clean features:

- Normalized DY
- Normalized P/VP
- Dividend Consistency (binary: 1 or 0)

I scaled and combined them with weighted averages:

```python
df['score'] = 0.5 * df['dy_norm'] + 0.3 * df['p_vp_norm'] + 0.2 * df['dividend_consistency']
```

You can visualize the distribution:

```python
df['score'].hist(bins=20)
```

> 🏆 **Figure 3** – Distribution of REIT scores

This gave me a ranked list that reflects yield, fair pricing, and reliability. Not flashy—just functional.

---

## Step 7 – Interpreting Results

At this point, I don’t treat the top fund as a buy signal. I treat the list as a shortlist. Then I do a second layer of qualitative checks:

- What's the fund’s asset mix?
- What’s their debt level?
- Do they have concentration risk in tenants?

Still, this data pipeline saves me hours every month.

---

## Final Words

This project didn’t just help me pick better REITs—it helped me understand how they behave. Building your own tools forces clarity. You stop outsourcing conviction.

And in finance, conviction matters.

> 🛠️ **Next Steps**:
>
> - Build a Streamlit app around this
> - Add historical performance and volatility
> - Track month-over-month DY evolution

I’ll be publishing the full codebase soon. Until then, start small: get the data, clean it, and question what you see.
