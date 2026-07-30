# Business Requirements Document (BRD)

**Project Title:** E-Commerce Checkout & Retention Optimization  
**Target Domain:** E-Commerce, Digital Conversion, & Customer Retention  
**Document Owner:** Business Systems Analyst  
**Document Version:** 1.0 (Final Baseline Specification)  

> **Portfolio Context Note:**  
> This document represents a self-directed Business Analysis Case Study designed to simulate end-to-end enterprise software requirements. Generative AI was utilized as an interactive sounding board to stress-test edge cases, simulate developer constraints, and validate non-functional compliance boundaries.

---

## 1. Document Control & Metadata

| Field | Details |
| :--- | :--- |
| **Project Sponsor** | VP of E-Commerce & Product Strategy |
| **Target Delivery** | Q4 Sprint Backlog Allocation |
| **Document Status** | Approved for Engineering & QA Hand-off |
| **Target Audience** | Software Engineers, Product Managers, QA Leads, Solution Architects |

---

## 2. Business Case & Strategic Objectives

### 2.1 Problem Statement
The e-commerce checkout funnel currently experiences a **70% cart abandonment rate**, primarily driven by third-party API latency during dynamic shipping calculation, a lack of automated re-engagement for idle sessions, and unoptimized point redemption controls. This results in lost transaction revenue and elevated Customer Acquisition Costs (CAC).

### 2.2 Strategic Key Performance Indicators (KPIs)
* **KPI-01 (Sales Conversion):** Increase overall store checkout sales conversion rate by **20%** within 6 months of release.
* **KPI-02 (Cart Recovery):** Recover **50%** of high-value abandoned carts ($\ge \$10.00$ subtotal) via automated re-engagement workflows.
* **KPI-03 (Checkout Reliability):** Protect checkout completion by enforcing automated circuit-breaker fallbacks during third-party API latency.

---

## 3. Scope & System Stakeholders

### 3.1 In-Scope
* Guest checkout optimization and post-purchase 1-click account conversion.
* Dynamic shipping calculation with a strict 3.0-second API timeout fallback.
* Automated cart abandonment identification, post-idle subtotal re-checks, and 7-day frequency suppression.
* Loyalty point earning schedules, redemption threshold gates, and provisional database state locking with automated release timers.

### 3.2 Out-of-Scope
* Core product catalog search and product detail page (PDP) visual redesigns.
* Direct payment gateway contract renegotiation or custom payment engine builds.
* Manual marketing asset and email graphics creation within external SaaS platforms (Klaviyo/Yotpo).

### 3.3 Core System Actors

| Actor | Type | Operational Role |
| :--- | :--- | :--- |
| **Guest Shopper** | Primary | Unauthenticated user executing a checkout session without password creation. |
| **Registered Shopper**| Primary | Authenticated user utilizing saved address data, earning, and redeeming reward points. |
| **Payment Gateway** | Secondary (External) | Financial processor handling credit card authorization and fraud checks. |
| **Carrier API** | Secondary (External) | Logistics service (FedEx/UPS/USPS) returning dynamic shipping rates based on postal code and package weight. |
| **Email SaaS (Klaviyo)**| Secondary (External) | Marketing automation engine receiving JSON payloads to trigger email recovery workflows. |
| **Loyalty SaaS (Yotpo)**| Secondary (External) | External points management platform syncing user reward balances. |

---

## 4. Functional Business Rules

### BR-01: Cart Abandonment Engine & Frequency Suppression
* **BR-01.1 (Inactivity Timer):** A cart session enters an "Abandoned" evaluation state after **120 consecutive minutes** of user inactivity following email entry.
* **BR-01.2 (Subtotal Minimum Gate):** Only carts with an active product subtotal $\ge \$10.00$ (excluding tax and shipping) qualify for recovery processing.
* **BR-01.3 (Post-Timer Re-evaluation):** At minute 120, the system must query real-time database state to re-evaluate the cart subtotal. If item removals dropped the subtotal below $10.00 during the idle window, the trigger terminates silently without emitting a payload.
* **BR-01.4 (Frequency Suppression Cap):** Prior to emitting an email payload, the system queries the customer email activity log. If an abandonment recovery payload was emitted for that email address within the last **7 rolling days**, the payload is suppressed, and a `Suppressed: Frequency Cap` event is logged internally.
* **BR-01.5 (Timer Cancellation):** Completing checkout or emptying the cart immediately terminates any active background inactivity timers.

### BR-02: Loyalty Program & Provisional State Lock (BR-02.1)

#### Point Valuation Math
$$\text{Value per Point} = \frac{\$5.00}{100 \text{ points}} = \$0.05 \text{ per point}$$

* **BR-02.1 (Earning Rate & Schedule):** Registered shoppers earn **1 point per $1.00 spent** on qualifying product subtotals. Points credit to the user's active balance exactly **24 hours post-shipment confirmation**.
* **BR-02.2 (Redemption Valuation):** Loyalty points redeem at a fixed rate of **100 points = $5.00 discount**.
* **BR-02.3 (Order Minimum Gate):** Point redemption requires a minimum cart product subtotal of **$30.00** (excluding tax and shipping).
* **BR-02.4 (Provisional State Lock):** Toggling "Redeem Points" at checkout places a provisional `HOLD` state on those points in the database to prevent double-spending across multiple open browser tabs.
* **BR-02.5 (Provisional Hold Expiration Worker):** A **30-minute session timer** governs provisional locks. If checkout completes, points move to `PERMANENTLY_DEDUCTED`. If the checkout is abandoned or remains idle for 30 minutes, an asynchronous background job releases the hold and restores points to the active balance.

### BR-03: Dynamic Shipping Rates & Timeout Fallback
* **BR-03.1 (Dynamic Rate Lookups):** Checkout triggers a real-time Carrier API lookup using the destination 5-digit postal code and cart package metrics. Registered shoppers auto-trigger this lookup on page load using their saved default shipping address.
* **BR-03.2 (3.0s Timeout Fallback):** The system enforces a strict **3.0-second API timeout**. If the Carrier API fails or exceeds 3.0 seconds, the system automatically applies a **$5.99 flat-rate standard shipping fee**.
* **BR-03.3 (Silent Error Logging):** Fallback events trigger a high-priority alert to backend error monitoring systems. No technical error message is displayed to the customer.
* **BR-03.4 (Late Payload Discard):** Response payloads from the Carrier API arriving *after* the 3.0-second threshold must be silently discarded for that active session to prevent unexpected price shifts on screen.

### BR-04: Guest Checkout & Account Conversion
* **BR-04.1 (Frictionless Guest Flow):** Guest shoppers can complete checkout by supplying contact email, shipping address, and payment details without password setup.
* **BR-04.2 (1-Click Conversion):** Upon reaching the Order Confirmation screen, the system renders an optional prompt allowing the guest to input a password and convert their guest profile to a registered account, linking the completed order history.

---

## 5. Non-Functional Requirements (NFRs)

* **NFR-01 (Security & PCI-DSS Compliance):** The checkout application must strictly comply with **PCI-DSS Level 1** guidelines. Primary Account Numbers (PAN) and card verification values (CVV) must never be stored in persistent internal databases (`Orders DB`). All card transactions must be processed via client-side payment gateway tokenization.
* **NFR-02 (Performance & Latency):** Initial checkout page load time must render within **1.5 seconds** on 4G mobile connections. The circuit-breaker timer for external shipping API calls must hard-cap wait times at **3.0 seconds**.
* **NFR-03 (System Availability):** Core checkout processing and cart session engines must maintain **99.9% operational uptime** during high-traffic promotional periods.
* **NFR-04 (Scalability):** The background cleanup worker (handling provisional point releases and idle cart timer queries) must scale horizontally to process up to 10,000 concurrent session evaluations per minute.

---

## 6. Assumptions & Edge Case Mitigations

| Functional Area | Identified Risk / Edge Case | Defined Mitigation Strategy |
| :--- | :--- | :--- |
| **Carrier API Outage** | Third-party shipping API experiences prolonged downtime. | Automated $5.99 fallback (BR-03.2) ensures checkout completion is never blocked by external vendor failures. |
| **Multi-Tab Redemption** | User attempts to redeem the same point balance on two browser tabs simultaneously. | Provisional database state lock (BR-02.4) instantly reserves points upon selection. |
| **Late API Payloads** | Dynamic shipping API responds after 5 seconds while user is interacting with the page. | Frontend explicitly discards late payloads arriving post-timeout to prevent unexpected price recalculations. |
| **Cart Subtotal Fluctuations**| User abandons a $40 cart, but drops it to $5 right before leaving the tab. | Post-timer re-evaluation (BR-01.3) verifies cart subtotal at minute 120 before dispatching recovery emails. |