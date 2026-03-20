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

This is a community fork with two key improvements:

1. **Dark Trading Aesthetic** — Black background with orange/red accents (`#ff4500`) optimized for market analysis dashboards
2. **100% English UI** — All 14 Vue components translated from Chinese to English for global accessibility

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

## 📊 Screenshots & Contributions

### Graph Visualization (Dark Theme)
<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/graph_dark.png" alt="Graph Relationship Visualization" width="48%"/>

### UI Overview (Dark Theme)
<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/ui_dark.png" alt="Dark Theme UI" width="48%"/>

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
