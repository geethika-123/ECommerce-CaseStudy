# E-Commerce Checkout & Retention Optimization | BA Case Study

![Domain](https://img.shields.io/badge/Domain-E--Commerce%20%26%20Fintech-blue)
![Role](https://img.shields.io/badge/Role-Business%20Systems%20Analyst-green)
![Frameworks](https://img.shields.io/badge/Frameworks-UML%20%7C%20Agile%20%7C%20Gherkin%20%7C%20INVEST-orange)

## 1. Project Overview & Business Context

An enterprise e-commerce retailer faced a **70% checkout cart abandonment rate**, leading to lost revenue and inflated customer acquisition costs. 

This case study demonstrates the end-to-end Business Analysis lifecycle used to diagnose checkout friction, define functional recovery mechanisms, and translate high-level business goals into sprint-ready engineering backlog items.

### Strategic Objectives (KPIs)
* **Sales Conversion:** Increase store checkout conversion by **20%**.
* **Cart Recovery:** Automatically recover **50%** of high-value abandoned carts ($\ge \$10.00$).
* **Checkout Reliability:** Eliminate customer drop-off caused by third-party API latency.

---

## 2. Scope & Key Functional Focus Areas

The initiative focused on four core functional modules:

1. **Automated Cart Recovery Engine (BR-01):** Evaluates idle cart sessions after 120 minutes, verifies subtotal guardrails, and enforces a **7-day frequency suppression cap** to prevent customer email fatigue.
2. **Loyalty Program Integration (BR-02 & BR-02.1):** Manages point earning rules and checkout redemption ($100\text{ points} = \$5\text{ off}$). Defines a **30-minute provisional state lock** to prevent double-spending across browser tabs.
3. **Dynamic Shipping & Circuit-Breaker Fallback (BR-03):** Enforces a strict **3.0-second timeout** on live shipping API lookups. If an API delays, the system silently applies a **$5.99 flat-rate fallback** to keep the customer moving through checkout.
4. **Frictionless Guest Checkout (BR-04):** Removes mandatory sign-up barriers while offering a 1-click account conversion prompt on the order confirmation screen.

---

## 3. Business Analysis Frameworks Applied

```text
┌──────────────────────────────────────────────────────────────────────────────────┐
│ BA METHODOLOGY FLOW                                                              │
├──────────────────────────────────────────────────────────────────────────────────┤
│ 1. Business Requirements  ──► Defined KPIs, guardrails, and non-negotiables      │
│ 2. Process & Data Models  ──► Visualized logic via UML Activity, Sequence, & DFD │
│ 3. Agile Slicing          ──► Sliced features into INVEST-compliant User Stories │
│ 4. Testability (Gherkin)  ──► Wrote Given-When-Then criteria for QA verification │
│ 5. Traceability (RTM)     ──► Mapped every backlog item back to strategic KPIs   │
└──────────────────────────────────────────────────────────────────────────────────┘
```
