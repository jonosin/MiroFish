<div align="center">

# MiroFish Extended-Safety

**Multi-Agent Social Simulation Engine with Safety & Reliability Focus**

A production-hardened fork emphasizing cost protection, state management, and session reliability.

<img src="https://raw.githubusercontent.com/jonosin/MiroFish/main/static/screenshots/upload_report_hero.png" alt="Upload Any Report, Simulate the Future" width="85%"/>

[![GitHub Stars](https://img.shields.io/github/stars/jonosin/MiroFish?style=flat-square&color=4CAF50)](https://github.com/jonosin/MiroFish/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/jonosin/MiroFish?style=flat-square)](https://github.com/jonosin/MiroFish/network)

</div>

---

## 🎯 Overview

**MiroFish** is a next-generation AI prediction engine powered by multi-agent technology. It extracts seed information from the real world (breaking news, policy drafts, research reports) and automatically constructs a high-fidelity parallel digital world. Within this space, thousands of agents with independent personalities, long-term memory, and behavioral logic interact freely and evolve socially.

Through **God's Eye** interaction, you can dynamically inject variables and precisely simulate future scenarios — **let the future unfold in a digital sandbox before decisions are made**.

### How It Works

Simply:
- **Upload** seed materials (research reports, documents, or interesting scenarios)
- **Describe** your prediction needs in natural language
- **Receive** a detailed prediction report and a deep-interactive high-fidelity digital world

---

## ✨ What's Different: Extended-Safety

This fork prioritizes **safety, reliability, and cost control** to prevent accidental token consumption and enable robust session management.

### 🔒 Core Safety Improvements

**Problem Solved:** Original MiroFish initialized expensive operations (persona generation, simulations) automatically on navigation, with no way to stop mid-way.

**Solution:**
1. **Navigation-Level Confirmation Modals** — All token-consuming actions require explicit confirmation **before** component initialization
   - Entering Step 2 (Env Setup): Confirm before persona generation starts
   - Entering Step 3 (Simulation): Confirm before simulation rounds begin
   - Clear token cost estimates in every modal

2. **History-Aware Flows** — Navigating from History to an unset-up simulation shows a cost warning
   - Auto-skip confirm if simulation already has environment setup complete
   - Prevents accidental re-initialization and double token consumption

3. **Graceful Cancellation & Resume** — Stop persona generation mid-way and resume later
   - Backend `_prepare_cancel_flags` system cleanly exits generation loop
   - Resume button appears after stopping — no re-confirmation needed
   - Partial progress preserved when possible

4. **Accurate Session Status** — History cards show true simulation state
   - Status badges: `Ready`, `Stopped`, `Preparing`, `Not Started`, `Error`
   - Environment setup status: `◈ Env Ready` (green) vs. `◈ Env Not Set Up` (muted)
   - Progress counts: "Stopped (12/40)", "Completed (40/40)", etc.

5. **Stale Simulation Cleanup** — One-click deletion with cascade cleanup
   - Delete any simulation from history
   - Cascades to: profiles, configs, reports, SQLite DBs, run logs, interaction history
   - Prevents orphaned simulations from accumulating

### 📊 Backend State Management

**Problem Solved:** Status mismatches between simulation state and UI; accidental initialization on mount; phase tracking was off-by-one.

**Solution:**
- **Accurate Phase Tracking** — Step indicators (01, 02, 03) properly reflect state
  - No "Initializing" flash when component mounts post-simulation creation
  - Phase starts at 1 for env setup (simulation already exists)

- **Stopped State Persistence** — `isStopped` flag preserved across navigation and resume flows

- **Graph Build Error Recovery** — Partial success handling
  - If graph build fails after creating Zep graph group: `graph_id` is saved
  - Simulation can proceed to Steps 2-3 without full graph data
  - Zep enrichment gracefully falls back to empty results

### 🌐 Full English UI

All user-facing text translated to English:
- Step labels, button text, modals, feedback messages
- Status indicators and progress descriptions
- Confirmation dialogs with clear action descriptions

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
   # Edit .env with your LLM API keys (Qwen, Claude, etc.)
   ```

2. **Install dependencies**
   ```bash
   npm run setup:all
   ```

3. **Start frontend + backend**
   ```bash
   npm start
   ```

   Frontend runs on `http://localhost:3001`
   Backend runs on `http://localhost:5001`

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

- **Confirmation-Gated Operations** — Explicit control over all API calls; no hidden initialization
- **Full English UI** — All menus, labels, guidance, and error messages in English
- **Dual LLM Slots** — Separate quality models for persona creation/reporting vs. simulation rounds (cost optimization)
- **History Tracking** — Browse, manage, and replay previous simulations with accurate status
- **Agent Interaction** — Deep conversation with simulated agents via God's Eye
- **Session Recovery** — Resume stopped operations without re-confirmation or re-cost

---

## 📋 Improvements & Fixes

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
- ✅ End-to-end simulation runs with cost tracking

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
- Backend safety and state management improvements
- Cost control and quota awareness
- Session reliability and recovery
- UI/UX clarity and English localization
- Error handling and graceful degradation
- Documentation and examples

---

## 📝 License

Based on [original MiroFish](https://github.com/666ghj/MiroFish)

---

**Built for reliability. Designed for control.**
