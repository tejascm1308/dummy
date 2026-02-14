# KUBERA — AI-Powered Stock Research Assistant for Indian Retail Investors

---

## Problem Statement

India has over **10 crore (100 million)** active demat accounts, and the number is growing rapidly — nearly doubling every two years. A significant majority of these are **first-time retail investors** with limited knowledge of stock market analysis. Yet, the tools available to them are either too complex (designed for professionals) or too superficial (generic tips on social media).

The core problem is this: **Retail investors in India lack an accessible, intelligent, and comprehensive tool to conduct pre-investment research on stocks.**

Today, if an investor wants to analyze a stock before buying, they must:

- Visit 4-5 different websites for financial data, news, technical charts, and governance information
- Manually interpret complex financial statements
- Rely on unverified social media tips or expensive advisory services
- Have no structured way to compare stocks, track upcoming IPOs, or assess portfolio risk

This fragmented and overwhelming process directly impacts **retail investors, first-time market participants, small-town investors, and anyone who cannot afford professional advisory services** — essentially the majority of India's growing investor base.

**KUBERA aims to solve this by building a conversational AI assistant that acts as a personal stock research analyst — one that any investor can simply talk to in plain English and get comprehensive, data-backed analysis without needing financial expertise.**

---

## Motivation

Three key observations motivate this solution:

### 1. The Information Asymmetry Gap

Institutional investors and HNIs (High Net Worth Individuals) have access to Bloomberg terminals, professional analysts, and dedicated research teams. Retail investors have Google and YouTube. This gap means retail investors often make decisions based on incomplete or outdated information, leading to poor investment outcomes.

### 2. The Complexity Barrier

Existing tools like Screener.in, Moneycontrol, or Trendlyne provide valuable data, but they require users to **already know** what they're looking for. A first-time investor doesn't know what P/E ratio means, let alone how to compare it across sectors. Natural language interaction removes this barrier entirely — a user can simply ask _"Is this stock expensive compared to its competitors?"_ instead of manually looking up valuation multiples.

### 3. The Trust Problem

Social media is filled with unqualified stock "tips" (often pump-and-dump schemes). KUBERA addresses this by strictly presenting **factual, data-backed analysis** from verified sources (NSE, BSE, regulatory filings) without ever recommending buy/sell/hold actions. It empowers users to make **informed decisions** rather than telling them what to do.

### 4. The Scattered Research Workflow

Even an experienced investor spends 30-60 minutes researching a single stock — jumping between websites for prices, financials, news, governance, and charts. KUBERA consolidates everything into a **single conversational interface**, reducing research time to under 5 minutes while covering more ground.

---

## Application

### Target Users

| User Segment                  | How KUBERA Helps                                                       |
| ----------------------------- | ---------------------------------------------------------------------- |
| **First-time investors**      | Ask questions in plain English, get easy-to-understand analysis        |
| **Working professionals**     | Quick research during breaks — no need to navigate multiple websites   |
| **Small-town investors**      | Access institutional-grade research without expensive subscriptions    |
| **Self-directed investors**   | Deep analysis tools (technicals, governance, comparisons) in one place |
| **Students learning finance** | Interactive way to understand stock fundamentals and market concepts   |

### Real-World Use Cases

**Use Case 1: Pre-Investment Research**

> _"I'm considering investing in Infosys. Can you give me a complete overview — financials, recent news, and how it compares to TCS?"_

The system fetches Infosys fundamentals (revenue, profit margins, P/E), recent news with sentiment analysis, and generates a side-by-side comparison chart with TCS — all in a single conversational response.

**Use Case 2: Portfolio Risk Assessment**

> _"I have holdings in Reliance, TCS, Infosys, Wipro, and HCL Tech. How risky is my portfolio?"_

The system analyzes the user's actual portfolio, identifies that 80% is concentrated in the IT sector, calculates overall portfolio beta, and presents a health score with diversification warnings — helping the investor understand concentration risk they may not have noticed.

**Use Case 3: IPO Research**

> _"Tell me about the upcoming Swiggy IPO — what do the financials look like?"_

The system pulls the prospectus financials, compares with listed peers (Zomato), shows subscription status, and presents the analysis in plain English — replacing the need to read a 400-page red herring prospectus.

**Use Case 4: Natural Language Stock Screening**

> _"Show me large-cap banking stocks with ROE above 15% and low debt"_

Instead of navigating complex filter interfaces, the user describes what they want in natural language, and the system converts it into structured filters, returning a table of matching stocks with key metrics.

**Use Case 5: Earnings Impact Analysis**

> _"HDFC Bank just reported quarterly results. What do the numbers show?"_

The system fetches the latest quarterly results, compares with previous quarter and year-ago quarter, checks against analyst estimates, and presents a clear summary of whether the company performed well or not.

**Use Case 6: Exportable Research**

> _"Generate a research report for Bharti Airtel and export it as PDF"_

The system compiles fundamentals, technicals, news sentiment, governance quality, and charts into a clean, branded PDF with legal disclaimers — a professional-grade report that the user can save offline or share with family members before making a collective investment decision.

### Where It Will Be Applied

- **Web application** accessible through any browser — desktop and mobile responsive
- **Potential mobile app** for on-the-go research during market hours
- Applicable across the **entire Indian listed equity universe** — 5,000+ stocks on NSE and BSE

---

## Proposed Method

### Architecture Overview

The solution uses a **Generative AI agent architecture** where a large language model (LLM) acts as the reasoning engine, connected to specialized data retrieval tools through the **Model Context Protocol (MCP)**.

```
User Query (Natural Language)
        │
        ▼
┌──────────────────────────────┐
│     LLM (GPT-4o-mini)        │
│  ┌──────────────────────┐    │
│  │  Understands intent   │    │
│  │  Plans tool calls     │    │
│  │  Synthesizes answer   │    │
│  └──────────────────────┘    │
└──────────────┬───────────────┘
               │ Decides which tools to call
               ▼
┌──────────────────────────────────────────────────────────────┐
│              MCP Tool Servers (Specialized)                    │
│                                                                │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────────────┐ │
│  │Financial │ │ Market & │ │News &    │ │ Visualization   │ │
│  │  Data    │ │Technical │ │Sentiment │ │   & Charts      │ │
│  │ Server   │ │ Server   │ │ Server   │ │    Server       │ │
│  └──────────┘ └──────────┘ └──────────┘ └─────────────────┘ │
│  ┌──────────┐ ┌──────────┐                                   │
│  │Governance│ │Portfolio │                                   │
│  │& Compli- │ │Analysis  │                                   │
│  │  ance    │ │ Server   │                                   │
│  └──────────┘ └──────────┘                                   │
└──────────────────────────────────────────────────────────────┘
               │
               ▼
        Structured Response
  (Text + Charts + Data Tables)
```

### How the AI Agent Works

1. **User sends a natural language query** via a chat interface (WebSocket for real-time streaming)
2. **The LLM interprets the query** — understands the intent, identifies which stocks are mentioned, and determines what data is needed
3. **The LLM selects and calls appropriate tools** — for example, if the user asks about "Reliance fundamentals and recent news," the LLM calls the Financial Data tool AND the News Sentiment tool
4. **Tools fetch real-time data** from external APIs and financial databases
5. **The LLM synthesizes** the raw data into a coherent, easy-to-understand response with relevant charts
6. **Response is streamed back** token-by-token for a ChatGPT-like experience

### Key GenAI Techniques

| Technique                                | How It's Used                                                                                           |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Agentic LLM Loop**                     | The LLM autonomously decides which tools to call and in what order, iterating if needed                 |
| **Tool Augmented Generation**            | Instead of relying on training data (which can be outdated), the LLM fetches live data through tools    |
| **Context Window Management**            | User's portfolio data and conversation history are injected into LLM context for personalized responses |
| **Sentiment Analysis**                   | Keyword-based and LLM-enhanced sentiment scoring on news articles                                       |
| **Natural Language to Structured Query** | For the stock screener — converting plain English into structured filter parameters                     |
| **Automated Report Generation**          | LLM generates structured research narratives from raw financial data                                    |

### Core Feature Modules

#### Module 1: Watchlist with Price Alerts

A monitoring layer where users track stocks they're researching. Custom alerts (price breach, volume spike, 52-week events) trigger email and in-app notifications. The AI provides context for each alert — not just _"Reliance crossed ₹2,800"_ but _"Reliance crossed ₹2,800, up 3.2% today. Recent news: Q3 results beat estimates."_

#### Module 2: Portfolio Health Score & Risk Dashboard

A quantitative scoring system (0-100) that evaluates the user's portfolio across four dimensions: diversification (sector concentration), quality (governance scores of holdings), valuation (relative to peers), and risk (portfolio beta and correlation). Visual dashboard includes sector allocation, correlation heatmap, and "what-if" stress scenarios.

#### Module 3: IPO Research Center

Aggregates upcoming IPO data (dates, price bands, lot sizes), financial analysis from prospectus data, peer comparison with listed companies, and live subscription status from exchange data. The AI generates a plain-English summary of each IPO's financial health.

#### Module 4: PDF Research Report Export

One-click generation of professional research reports from chat conversations or standalone queries. Includes company overview, key metrics, embedded charts, news summary, and technical snapshot — all with proper disclaimers. Uses ReportLab/WeasyPrint for PDF generation.

#### Module 5: Natural Language Stock Screener

Converts plain English queries into structured filters against a stock universe database. Users describe criteria in natural language, and the system returns matching stocks with relevant metrics. Supports both predefined screens ("Top Dividend Stocks") and custom queries.

#### Module 6: Earnings Calendar & Results Tracker

Tracks quarterly result dates, fetches published results, and generates AI-powered analysis comparing actual numbers with previous periods and analyst estimates. Background jobs auto-detect newly published results and make them available for analysis.

---

## Datasets / Data Sources

| Data Category                             | Source                                   | Availability        | Type      |
| ----------------------------------------- | ---------------------------------------- | ------------------- | --------- |
| **Stock Prices (Real-time & Historical)** | Yahoo Finance API                        | Free, Public        | REST API  |
| **Company Fundamentals**                  | Yahoo Finance, NSE/BSE Filings           | Free, Public        | REST API  |
| **Financial Statements**                  | Yahoo Finance (quarterly/annual)         | Free, Public        | REST API  |
| **News Articles**                         | NewsAPI, Yahoo Finance RSS               | Free tier available | REST API  |
| **Analyst Ratings & Estimates**           | Yahoo Finance, Finnhub                   | Free tier available | REST API  |
| **Technical Indicators**                  | Calculated from price data using TA-Lib  | Derived (computed)  | Library   |
| **Corporate Governance**                  | Yahoo Finance (insider trades, holdings) | Free, Public        | REST API  |
| **IPO Data**                              | NSE/BSE official feeds, SEBI filings     | Public (regulatory) | Web + API |
| **Mutual Fund Holdings**                  | AMFI (Association of MFs in India)       | Public (regulatory) | CSV/API   |
| **Shareholding Patterns**                 | NSE/BSE quarterly filings                | Public (regulatory) | Web + API |
| **Earnings/Results Data**                 | NSE/BSE corporate announcements          | Public (regulatory) | Web + API |

### Data Characteristics

- **Volume**: 5,000+ listed stocks on NSE/BSE, each with 10+ years of historical data
- **Frequency**: Price data updated every 30 minutes; financials updated quarterly; news fetched on-demand
- **Format**: JSON responses from APIs, CSV from regulatory sources, HTML from exchange websites
- **No proprietary or paid data required** — the entire system runs on publicly available, free-tier data sources, making it accessible and scalable without significant data costs

---

## Experiments

### Validation Strategy

The solution will be validated through both **quantitative metrics** and **qualitative user testing**.

### Quantitative Evaluation

| Metric                          | What It Measures                                                    | Target             |
| ------------------------------- | ------------------------------------------------------------------- | ------------------ |
| **Query Resolution Rate**       | % of user queries the AI answers correctly with relevant data       | > 90%              |
| **Tool Selection Accuracy**     | Does the AI call the right tools for the query?                     | > 95%              |
| **Response Latency**            | Time from query to first token of response (streaming)              | < 3 seconds        |
| **Data Freshness**              | How current is the financial data served?                           | < 30 min delay     |
| **Portfolio Score Consistency** | Does the health score change proportionally with portfolio changes? | Correlation > 0.85 |
| **Screener Accuracy**           | Does the NL screener return correct stocks matching the criteria?   | > 92% precision    |
| **Alert Delivery Rate**         | % of triggered alerts successfully delivered to users               | > 99%              |

### Experiment 1: Query Understanding Test

- Feed 200+ diverse stock queries (fundamentals, comparison, news, technical, portfolio) to the system
- Measure: Does the AI correctly identify the intent and call appropriate tools?
- Baseline comparison: Compare with a simple keyword-matching approach

### Experiment 2: Research Quality Comparison

- Generate research reports for 20 popular stocks using KUBERA
- Compare the coverage (metrics included, data accuracy) against manual research from Screener.in + Moneycontrol + Trendlyne combined
- Metric: Information completeness score (% of key metrics covered)

### Experiment 3: Portfolio Health Score Validation

- Create 50 test portfolios with known characteristics (concentrated, diversified, high-risk, low-risk)
- Verify that the health score correctly ranks them (a well-diversified, high-quality portfolio should score higher)
- Statistical test: Spearman rank correlation between expected and actual scores

### Experiment 4: Screener Accuracy Test

- Prepare 50 natural language screening queries with known correct results
- Compare KUBERA's output against manually verified stock lists
- Metrics: Precision, recall, F1 score

### Experiment 5: User Experience Test

- Onboard 20 real users (mix of beginners and experienced investors)
- Measure: Time taken to research a stock using KUBERA vs. traditional method
- Collect: Net Promoter Score (NPS), ease-of-use ratings, feature satisfaction

---

## Novelty and Scope to Scale

### What Makes This Unique

**1. First AI-Native Research Platform for Indian Markets**
While tools like Screener.in and Moneycontrol serve data, they are **data-first platforms** — users must know what to look for. KUBERA is **AI-first** — users describe their need in plain English, and the AI figures out what data to fetch, how to analyze it, and how to present it. This fundamental difference makes stock research accessible to millions of first-time investors who don't speak "finance."

**2. Portfolio-Aware AI**
Unlike generic AI chatbots (ChatGPT, Gemini), KUBERA's AI has access to the user's **actual portfolio data**. This enables deeply personalized analysis — not just "What is TCS?" but "How does TCS fit into _your_ portfolio? You already have 3 IT stocks — adding TCS increases your sector concentration to 65%." No existing Indian platform combines AI chat with live portfolio awareness.

**3. Governance-Integrated Analysis**
Most retail tools focus on price and financials. KUBERA uniquely integrates **corporate governance metrics** — promoter holding, pledging, insider transactions, board independence, audit quality — into every analysis. These are critical risk signals that retail investors typically miss but institutional investors always check.

**4. Natural Language Stock Screener**
Existing screeners require knowledge of specific metric names, ranges, and filter interfaces. KUBERA allows screening in everyday language: _"Find me companies that consistently increase dividends and have low debt."_ The AI translates this into structured queries, dramatically lowering the entry barrier for stock discovery.

**5. IPO Research with AI Analysis**
No existing platform offers AI-powered IPO analysis for Indian markets. Users currently read 400-page prospectuses or rely on social media opinions. KUBERA extracts key financials, compares with listed peers, and presents a clear, factual summary — making IPO research accessible to everyone.

### Scope to Scale

| Dimension           | Current Scope               | Scaling Potential                                                                                             |
| ------------------- | --------------------------- | ------------------------------------------------------------------------------------------------------------- |
| **Market Coverage** | Indian stocks (NSE/BSE)     | Expand to US (NYSE/NASDAQ), UK (LSE), and other emerging markets                                              |
| **Asset Classes**   | Equities only               | Add mutual funds, ETFs, bonds, commodities, and crypto                                                        |
| **Language**        | English                     | Add Hindi, Tamil, Telugu, and other Indian languages for tier-2/3 city adoption                               |
| **Platform**        | Web application             | Native mobile apps (iOS/Android) with push notification alerts                                                |
| **AI Model**        | GPT-4o-mini                 | Upgrade to larger models (GPT-4o, Claude) or fine-tune open-source models (Llama, Mistral) for cost reduction |
| **Data Sources**    | Free APIs                   | Integrate premium data (Bloomberg, Reuters) for institutional-grade accuracy                                  |
| **User Base**       | Individual retail investors | Expand to financial advisors, small brokerages, and fintech platforms via API/white-label                     |
| **Revenue Model**   | Free tier                   | Freemium (basic free, premium features paid), B2B API licensing for brokerages                                |

### Impact Potential

- **10 crore+ demat account holders** in India who could benefit from accessible stock research
- Reducing average stock research time from **30-60 minutes to under 5 minutes**
- Bridging the information gap between retail and institutional investors
- Enabling informed decision-making in **tier-2 and tier-3 cities** where financial literacy is lowest but investor growth is fastest
- Creating a platform that can evolve into a **comprehensive financial research ecosystem** beyond just stocks

---

## Technology Summary

| Component           | Technology                                                         |
| ------------------- | ------------------------------------------------------------------ |
| **Frontend**        | React, TypeScript, Vite, TailwindCSS                               |
| **Backend**         | Python, FastAPI (async), WebSocket                                 |
| **Database**        | PostgreSQL (hosted on Supabase)                                    |
| **AI/LLM**          | GPT-4o-mini via OpenRouter API                                     |
| **Tool Framework**  | Model Context Protocol (MCP) with FastMCP                          |
| **Charts**          | Plotly (interactive), uploaded to Supabase Storage                 |
| **PDF Generation**  | ReportLab / WeasyPrint                                             |
| **Background Jobs** | APScheduler (price updates, report generation, alerts)             |
| **Email**           | SMTP via aiosmtplib (alerts, reports, notifications)               |
| **Deployment**      | Vercel (frontend), Render (backend), Supabase (database + storage) |

---

> _KUBERA — Democratizing stock market research for every Indian investor, one conversation at a time._
