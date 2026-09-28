# ☕ Cafe Simulation – Operational Optimization with Arena

> Discrete-event simulation of a real café (Mono Cafe) built in **Arena Simulation Software**, used to analyze current performance and evaluate a staffing improvement scenario.

**Course:** CENG 4513 – Modeling and Simulation
**Department:** Computer Engineering
**Author:** Bartu Paçal
**Instructor:** İlke Tan

---

## 📌 Overview

This project models the order-to-delivery workflow of a café with **one cashier and one barista**. Field data was collected by direct observation, fitted to statistical distributions with the Arena Input Analyzer, and used to build two simulation models:

1. **Initial Model** – the café as it operates today (baseline).
2. **Improved Model** – a consolidated, cross-trained single-staff configuration.

The goal is to measure customer waiting times, resource utilization and throughput, and to check whether labor can be reduced without hurting service.

---

## 📁 Repository Contents

| File | Description |
|------|-------------|
| `InitialModel.doe` | Arena model of the baseline system (Cashier + Barista) |
| `ImprovedModel.doe` | Arena model of the optimized system (single Multi-Task Staff) |
| `CAFE_SIMULATION_REPORT.pdf` | Full project report (data, distributions, results, appendices) |

> Open the `.doe` files with **Arena Simulation Software** (Rockwell Automation; the Student edition is sufficient).

---

## 📊 Data Collection

- **Location:** Mono Cafe (single cashier, single barista, open-counter design)
- **Date:** 14.12.2025, 11:25:22 – 21:15:30 (**590 minutes**)
- **Sample:** 80 customer groups, tracked individually from arrival to exit
- **Recorded:** arrival time, group size, order type, order start/end, preparation start/end, exit time
- **Order types:** Drinks only (1), Food & Drinks (2), Food only (3)

---

## 📈 Input Data Analysis

Distributions were fitted with the **Arena Input Analyzer** (times converted to minutes).

| Variable | Distribution | Arena Expression |
|----------|--------------|------------------|
| Interarrival time | Exponential | `-0.001 + EXPO(7.38)` |
| Cashier (order) time | Lognormal | `LOGN(0.499, 0.389)` |
| Prep – Drinks only | Weibull | `WEIB(2.04, 1.7)` |
| Prep – Food & Drinks | Beta | `0.21 + 1.79 * BETA(0.00001, 0.00001)` |
| Prep – Food only | Beta | `4 * BETA(0.00001, 0.00001)` |
| Order type | Discrete | `DISC(0.925, 1, 0.95, 2, 1.0, 3)` |
| Group size | Discrete | `DISC(0.875, 1, 0.975, 2, 0.9875, 4, 1.0, 5)` |

> Food-related preparation samples are very small (n = 2 and n = 4), so those Beta fits are only indicative.

---

## 🧩 Model Description

### Initial Model
```
Customer Arrival → Assign Attributes → Cashier Process → Determine Order Type
                                                              │
                          ┌───────────────────┬───────────────┴──────────┐
                     Prep Drink        Prep Food and Drink          Prep Food
                          └───────────────────┴───────────────┬──────────┘
                                                          Exit System
```
- Resources: **Cashier (capacity 1)** and **Barista (capacity 1)**
- Routing by `order_type` using an N-way by Condition *Decide* module

### Improved Model
- Cashier and Barista merged into a single **MultiTaskStaff** resource (capacity 1)
- Preparation times reduced by **20%** (`0.8 * expression`) to represent experience and a better workstation layout
- **High priority** given to the *Cashier Process* so customers at the register are served first
- A *Record* module added to capture `Total_System_Time`

---

## ⚙️ Run Settings

| Setting | Value |
|---------|-------|
| Replications | 10 |
| Replication length | 590 minutes |
| Base time unit | Minutes |
| Warm-up period | 0 |
| Simulation type | Terminating (starts empty, ends at 590 min) |

---

## 🏁 Results

| Metric | Initial Model | Improved Model |
|--------|:-------------:|:--------------:|
| Staff count | 2 | **1** |
| Customers in / out | 85.70 / 85.60 | 85.60 / 85.40 |
| Cashier utilization | 7.37% | – |
| Barista utilization | 17.22% | – |
| Multi-Task Staff utilization | – | 21.08% |
| Avg. time in system | ≈ 1.80 min | ≈ 1.82 min |
| Avg. waiting time | 0.245 min | 0.320 min |

### Key Findings
- The baseline system is heavily **over-staffed** for the observed demand (staff idle ≈ 83–93% of the time).
- Merging roles gives a **50% reduction in staff** with practically the **same throughput** (≈ 85 customers per day).
- Time in system increases by only ~1 second; waiting time stays well below one minute.
- Even with one employee, utilization is ~21%, leaving ample buffer for demand spikes.

### Recommendation
Adopt the **Multi-Task Staff** configuration to reduce labor cost while keeping the same service level.

---

## ⚠️ Limitations

- Data comes from a **single observation day** (80 groups), so day-to-day variation is not captured.
- Arrivals are modeled with one stationary exponential rate, although lunch (12:00–13:30) and evening (18:30–20:00) peaks were observed.
- The 20% preparation speed-up is an **assumption**, not measured data.
- Group size is recorded but does not affect service times in the model.

---

## 🚀 How to Run

1. Install **Arena Simulation Software**.
2. Open `InitialModel.doe` or `ImprovedModel.doe`.
3. Check **Run → Setup** (10 replications, 590 minutes, base time unit: minutes).
4. Press **Run → Go** and view the report when the run completes.

---

## 🛠️ Tools

- Arena Simulation Software (Student License)
- Arena Input Analyzer
- Microsoft Excel (data preprocessing)

---

## 📚 Report

The complete methodology, Input Analyzer outputs, raw observation logs and processed datasets are available in [`CAFE SIMULATION REPORT.pdf`](./CAFE%20SIMULATION%20REPORT.pdf).
