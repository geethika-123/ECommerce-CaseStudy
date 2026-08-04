# **Requirements Traceability Matrix (RTM)**

**Project Title:** E-Commerce Checkout & Retention Optimization

**Document Owner:** Business Systems Analyst

**Document Version:** 1.0 (Baseline Specification)

## **1\. Traceability Purpose & Methodology**

The Requirements Traceability Matrix (RTM) establishes a bi-directional line of sight between overarching strategic business goals (KPIs), operational business rules, detailed functional specifications, sprint backlog items, and quality assurance acceptance criteria.

This guarantees **100% requirements coverage**—ensuring no backlog item is built without strategic justification, and no business requirement is left unverified by QA test scenarios.

**Traceability Hierarchy:**

Strategic KPI → Business Rule (BR) → Use Case (UC) → User Story → QA Test Case

## **2\. End-to-End Requirements Traceability Matrix**

| Strategic Goal / KPI | Business Rule ID | Functional Description | Use Case Mapping | Sprint Backlog Story ID | Target Acceptance Scenario (Gherkin) | QA Verification Status |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **KPI-03:** Checkout Reliability | **BR-03.1**, **BR-03.2** | Dynamic shipping API lookup with strict 3.0-second timeout and $5.99 flat-rate fallback application. | UC-01, UC-02 | US-01 | Scenario 1: Timeout triggers $5.99 fee Scenario 2: Successful order with fallback | Approved |
| **KPI-03:** Checkout Reliability | **BR-03.3**, **BR-03.4** | High-priority silent error logging and post-timeout late payload discarding. | UC-01, UC-02 | US-01 | Scenario 1: Silent backend error alert Scenario 5: Discard late response payload | Approved |
| **KPI-02:** Cart Recovery | **BR-01.1**, **BR-01.2** | 120-minute idle session evaluation with $10.00 subtotal minimum qualification gate. | UC-03 | US-02A | Scenario 1: Valid abandonment event trigger Scenario 2: Subtotal drop below $10 cancels event | Approved |
| **KPI-02:** Cart Recovery | **BR-01.3**, **BR-01.4** | Minute-120 database re-evaluation and 7-day rolling frequency suppression cap. | UC-03 | US-02A | Scenario 1: Real-time minute-120 re-check Scenario 3: 7-day frequency cap suppression | Approved |
| **KPI-02:** Cart Recovery | **BR-01.4** (SaaS) | Transmit validated recovery event payloads (cart items, points) via Klaviyo HTTP POST. | UC-03 | US-02B | Scenario 1: Successful JSON payload dispatch Scenario 2: HTTP 503 retry backoff | Approved |
| **KPI-01:** Sales Conversion | **BR-02.2**, **BR-02.3** | Loyalty point value calculation ($100\\text{ pts} \= \\$5$) and $30.00 order subtotal minimum redemption gate. | UC-04 | US-03A | Scenario 1: Successful point discount toggle Scenario 2: Disabled UI toggle on subtotals $\< \\$30$ | Approved |
| **KPI-01:** Sales Conversion | **BR-02.4**, **BR-02.5** | Provisional database point locking and 30-minute background release worker for idle sessions. | UC-04 | US-03B | Scenario 1: Database provisional hold creation Scenario 2: 30-minute hold release worker | Approved |
| **KPI-01:** Sales Conversion | **BR-04.1**, **BR-04.2** | Frictionless guest checkout processing with 1-click account conversion on order confirmation. | UC-02 | US-01 / Flow | UC-02 Main Success Scenario & Step 8 Account Prompt | Approved |

## **3\. Requirements Coverage & Gap Analysis Audit**

| Category | Target | Mapped | Coverage (%) |
| :---- | :---- | :---- | :---- |
| **Strategic KPIs** | 3 | 3 | 100.0% |
| **Functional Business Rules (BR)** | 12 | 12 | 100.0% |
| **Use Case Scenarios** | 4 | 4 | 100.0% |
| **Sprint Backlog Stories** | 5 | 5 | 100.0% |
| **QA Acceptance Scenarios (Gherkin)** | 12 | 12 | 100.0% |
| **TOTAL SYSTEM TRACEABILITY COVERAGE** | — | — | **100.0%** |

*   
  **Orphan Requirements Check:** 0 requirements identified without an associated strategic KPI.  
* **Uncovered Stories Check:** 0 user stories identified without explicit Gherkin QA test criteria.  
* **Testability Overrides:** Non-production environment override switches specified for timer-dependent rules (US-02A Scenario 4, US-03B Scenario 3\) to enable automated integration testing.

