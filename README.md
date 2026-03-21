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

## 📋 Improvements & Fixes (Extended-Safety Focus)

This fork addresses critical safety and reliability gaps in the original MiroFish to prevent accidental token consumption and enable robust session management.

### 🔒 Safety & Cost Protection

#### Navigation-Level Confirmation Modals
- **Prevent Accidental Initialization** — Confirmation modals appear **at the navigation level** (before component mount), not inside components
  - Entering Step 2 (Env Setup): Confirm before persona generation starts
  - Entering Step 3 (Simulation): Confirm before simulation rounds begin
  - Prevents the bug where env setup would initialize even after canceling
- **Clear Token Usage Estimates** — Modal shows approximate token cost and models being used
- **One-Click Confirmation** — No hidden flows; user controls exactly when expensive operations start

#### History-Aware Confirmation
- **History → Step 2 Aware** — Navigating from History to an unset-up simulation shows a confirmation modal warning of token cost
- **Skip Confirm if Env Ready** — If a simulation already has environment setup complete (`profiles_count > 0` and `config_generated`), auto-trigger without asking again
- **Prevents Double-Setup** — No accidental re-initialization of already-prepared simulations

### ⏸️ Cancellation & Resume

#### Graceful Persona Generation Stop
- **Stop Button During Env Setup** — Press "Stop Persona Generation" to halt mid-way with full cleanup
- **Cancellation Flag System** — Backend maintains `_prepare_cancel_flags` dict to cleanly exit the generation loop without partial state
- **Resume Without Re-Confirm** — After stopping, a green **▶ Resume Persona Generation** button appears
  - Click Resume to continue from where you stopped
  - No need to re-confirm or see the cost modal again
  - Preserves partial progress when possible

#### Simulation Round Cancellation
- **Stop Simulation Anytime** — Kill simulation runs at any round with immediate cleanup
- **No Orphaned Processes** — Simulation cancellation properly clears all polling and background tasks

### 📊 History & Session Management

#### Accurate Status Indicators
- **Status-Aware Badges** — History cards show true simulation state:
  - `Ready` (green) — Completed successfully or ready to run
  - `Stopped` (red) — User manually stopped, can resume
  - `Preparing` (yellow) — Env setup in progress
  - `Not Started` (grey) — Created but never initialized
  - `Error` (red) — Failed, shows error details
- **Progress Display** — Shows actual round counts: "Stopped (12/40)", "Preparing...", "Completed (40/40)", etc.
- **Env Status Icon** — `◈` icon on history cards:
  - Green `◈ Env Ready` — Environment fully set up, profiles generated
  - Muted `◈ Env Not Set Up` — Environment not initialized or stopped before completion

#### Complete File Tracking
- **All Uploaded Files Shown** — History cards display every file used in a simulation (not limited to 3)
- **File Metadata Preserved** — Tracks which documents contributed to each simulation for reproducibility

#### Stale Simulation Cleanup
- **Delete Simulation Cascade** — One-click deletion of any simulation from history
  - Removes simulation directory and all sub-directories
  - Cascades to delete associated reports
  - Clears in-memory cache and SQLite databases
  - Cleanup includes: agent profiles, simulation configs, run logs, interaction history, reports
- **History Pagination** — History now loads up to 100 simulations (configurable), sorted by recency with active/running simulations first
- **Safe Deletion** — Prompts with simulation ID for confirmation before permanent deletion

### 🎨 UI/UX Consistency

#### Dark Theme Completeness
- **Step 4 Report Dark Theme** — Report generation page fully matches dark trading aesthetic
  - Uses CSS custom properties for consistent coloring
  - Proper contrast ratios for readability
  - Orange accent colors for critical info (`#ff4500`)
- **Consistent Status Colors** — All status indicators use same color palette across all views

#### Fixed History Display
- **IntersectionObserver Animations** — History cards animate in as they scroll into view (proper threshold tuning)
- **Default State Handling** — Cards show correct initial state before data loads
- **Responsive Card Layout** — Grid adapts to viewport with proper spacing

### 🔧 Backend Improvements

#### State Management
- **Accurate Phase Tracking** — Step indicators (01, 02, 03, etc.) properly reflect actual state
  - No "Initializing" flash when component mounts after simulation already created
  - Phase numbering starts at 1 (not 0) for env setup since simulation already exists
- **Stopped State Persistence** — `isStopped` flag preserved across navigation and resume flows

#### Error Recovery
- **Graph Build Partial Success** — If graph build fails after creating Zep graph group:
  - `graph_id` is still saved to project (graph group created but no episodes added)
  - Simulation can proceed to Steps 2-3 without full graph data
  - Zep enrichment gracefully falls back to empty results
  - User can proceed to build/run simulation without graph visualization
- **Zep Quota Management** — Identifies and recovers from Zep episode limit errors with clear messaging

### 🧪 Testing & Verification

All improvements have been tested with:
- ✅ Multiple confirm/cancel flows in sequence
- ✅ History navigation to unset-up vs. set-up simulations
- ✅ Resume after stopping persona generation mid-way
- ✅ Deletion and cascade cleanup verification
- ✅ State consistency across page reloads
- ✅ Dark theme contrast and readability across all pages

---

## 🚀 Key Metrics

| Metric | Before | After |
|--------|--------|-------|
| Accidental token consumption | Possible (3-5 bugs) | Prevented (gated at nav) |
| Simulation reusability | No resume (re-confirm needed) | Full resume without re-confirm |
| History accuracy | Status mismatches, wrong progress | 100% accurate, status-first logic |
| Orphaned simulations | No visibility or cleanup | Full history view + 1-click delete |
| Graph build robustness | Fail completely | Partial success, proceed without data |

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
