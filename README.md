<div align="center">

# MiroFish — Dark Trading Theme Fork

**Multi-Agent Social Simulation Engine for Market Prediction**

A fully translated, dark-themed fork with trading aesthetics. Predict markets and simulate futures through collective intelligence.

<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/upload_report_hero.png" alt="Upload Any Report, Simulate the Future" width="85%"/>

[![GitHub Stars](https://img.shields.io/github/stars/jonosin/MiroFish?style=flat-square&color=DAA520)](https://github.com/jonosin/MiroFish/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/jonosin/MiroFish?style=flat-square)](https://github.com/jonosin/MiroFish/network)

</div>

---

## 🎯 Overview

**MiroFish** is a next-generation AI prediction engine powered by multi-agent technology. It extracts seed information from the real world (breaking news, policy drafts, market signals) and automatically constructs a high-fidelity parallel digital world. Within this space, thousands of agents with independent personalities, long-term memory, and behavioral logic interact freely and evolve socially.

Through **God's Eye** interaction, you can dynamically inject variables and precisely simulate future scenarios — **let the future unfold in a digital sandbox before decisions are made**.

### How It Works

Simply:
- **Upload** seed materials (research reports, documents, or interesting scenarios)
- **Describe** your prediction needs in natural language
- **Receive** a detailed prediction report and a deep-interactive high-fidelity digital world

---

## ✨ This Fork: Dark Trading Theme + Full English Translation

This is a community fork with multiple improvements:

1. **Dark Trading Aesthetic** — Black background with orange/red accents (`#ff4500`) optimized for market analysis dashboards
2. **100% English UI** — All 14 Vue components translated from Chinese to English for global accessibility
3. **Safety Controls** — Confirmation modals before all token-consuming actions, with visible stop buttons for persona generation and simulation runs
4. **Reliable History** — Fixed history cards display (IntersectionObserver threshold and default state)

All core MiroFish simulation logic remains unchanged. Perfect for traders and market researchers.

---

## 🚀 Quick Start

### Prerequisites

| Tool | Version | Check |
|------|---------|-------|
| **Node.js** | 18+ | `node -v` |
| **Python** | ≥3.11, ≤3.12 | `python --version` |
| **uv** | Latest | `uv --version` |

### Setup

1. **Configure environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your LLM API keys
   ```

2. **Install dependencies**
   ```bash
   npm run setup:all
   ```

3. **Start frontend + backend**
   ```bash
   npm start
   ```

   Frontend runs on `http://localhost:3000`

---

## 🔧 Architecture

| Phase | Function |
|-------|----------|
| **01 Graph Build** | Extract entities, build knowledge graph with GraphRAG |
| **02 Env Setup** | Generate agent personas, configure simulation environment |
| **03 Simulate** | Dual-platform parallel simulation, parse prediction needs |
| **04 Report** | ReportAgent generates detailed analysis and insights |
| **05 Deep Interact** | Chat with agents in the simulation or with ReportAgent |

---

## 🎨 Features

- **Dark Trading UI** — Orange accents on black background, built with CSS custom properties for easy theming
- **Full English** — All menus, labels, and guidance in English
- **Dual LLM Slots** — Separate quality models for persona creation/reporting vs. simulation rounds (cost optimization)
- **History Tracking** — Browse and replay previous simulations
- **Agent Interaction** — Deep conversation with simulated agents via God's Eye

---

## 🛡️ Safety & Reliability (Recent Updates)

### Confirmation & Cost Protection
- **Token Cost Protection** — All API-consuming actions (graph build, persona generation, simulation rounds, report generation) require explicit confirmation before execution
- **Navigation-Level Gating** — Confirmation modals appear **before** you navigate to token-consuming steps. Nothing initializes until you explicitly confirm
- **History → Step 2 Confirm** — When navigating from History to an unset-up simulation, a confirmation modal warns you and shows token usage estimates

### Cancellation & Resume
- **Cancellable Persona Generation** — Stop button during environment setup. Pressing Stop mid-way pauses generation with full cleanup
- **Resume Without Re-Confirm** — After stopping, a green **Resume Persona Generation** button appears. Click to resume without needing another confirmation
- **Simulation Cancellation** — Stop simulation rounds anytime with immediate cleanup

### History & Data Management
- **Accurate Status Badges** — History cards now show:
  - `◈ Env Ready` (green) — Environment already set up, profiles generated
  - `◈ Env Not Set Up` (muted) — Environment not started or stopped mid-setup
  - Progress indicator: "Stopped (N/M)", "Preparing...", "Completed", "Not Started", etc.
- **Complete File Display** — History cards show all uploaded files (not limited to 3)
- **Delete Simulation** — Right-click or use the Delete button in history modal to permanently remove a simulation and all associated data (profiles, configs, reports, logs)

### Theme & UX
- **Dark Theme Report Page** — Step 4 Report page now fully matches the dark trading aesthetic with proper contrast and styling
- **Fixed History Display** — History cards display correctly with proper animation and state management

---

## 📊 Screenshots & Contributions

### Graph Visualization (Dark Theme)
<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/graph_dark.png" alt="Graph Relationship Visualization" width="48%"/>

### UI Overview (Dark Theme)
<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/ui_dark.png" alt="Dark Theme UI" width="48%"/>

---

## 📋 Recent Fixes & Improvements (Current Session)

### UX & Safety Enhancements
✅ **History → Step 2 Confirmation Modal** — Navigating from History to an unset-up simulation now shows a confirmation modal before initialization, preventing accidental token consumption
✅ **Env Status Differentiation** — History cards now visually distinguish which simulations have environments set up (green `◈ Env Ready`) vs. not set up (grey `◈ Env Not Set Up`)
✅ **Resume Button** — Stop persona generation and resume later without re-confirming (click the green Resume button)
✅ **Dark Theme Report Page** — Step 4 Report page fully converted to dark theme with proper CSS vars and contrast
✅ **Delete Simulation** — Permanently remove simulations from history with one click. Cascades to delete all associated data (profiles, configs, reports, SQLite DBs, logs)

### Technical Improvements
- Improved state management for stopped vs. running simulations
- Proper cleanup on simulation deletion (file deletion + report cleanup + in-memory cache removal)
- Better visual feedback for env setup status across history cards
- Consistent dark theme implementation across all UI components

---

## 🤝 Contributing

Contributions welcome! This fork focuses on:
- UI/UX improvements for trading workflows
- Model optimization (cheaper inference, better quality)
- Documentation and examples
- Integration with market data APIs

---

## 📝 License

Based on [original MiroFish](https://github.com/666ghj/MiroFish)

---

**Made for traders. Built for the future.**
