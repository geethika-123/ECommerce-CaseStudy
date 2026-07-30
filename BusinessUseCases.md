# System Boundary & Functional Use Case Specifications

**Project Title:** E-Commerce Checkout & Retention Optimization  
**Target Domain:** E-Commerce, System Boundaries, & Actor Interactions  
**Document Owner:** Business Systems Analyst  
**Document Version:** 1.0 (Final Baseline Specification)  

> **Portfolio Context Note:**  
> This document represents a self-directed Business Analysis Case Study designed to simulate end-to-end enterprise software requirements. Generative AI was utilized as an interactive sounding board to stress-test edge cases, simulate developer constraints, and validate non-functional compliance boundaries.

---

## 1. System Boundary Overview

The system boundary encompasses all checkout processing, customer profile identification, payment gateway interactions, cart state timers, dynamic shipping lookup handlers, and marketing/loyalty integration hooks. 

Components outside the boundary include third-party API internal operations (Carrier algorithms, Payment Gateway clearing houses, Klaviyo campaign email render engines).

```mermaid
graph TB
    subgraph Inside System Boundary ["System Boundary: E-Commerce Checkout & Retention Engine"]
        UC1[UC-01: Calculate Dynamic Shipping Rates]
        UC2[UC-02: Process Guest Checkout & Payment]
        UC3[UC-03: Trigger Abandoned Cart Recovery Payload]
        UC4[UC-04: Redeem Loyalty Points at Checkout]
    end

    Guest[Guest Shopper] -->|Initiates| UC2
    Registered[Registered Shopper] -->|Initiates| UC1
    Registered -->|Initiates| UC4
    
    UC1 <-->|ZIP & Cart Weight| Carrier[Carrier Shipping API]
    UC2 <-->|Payment Payload| Gateway[Payment Gateway]
    UC2 -- Includes --> UC1
    UC2 -. Extends on 2-hr Idle .-> UC3
    
    UC3 -->|Emit Recovery Payload| Klaviyo[Klaviyo Email SaaS]
    UC4 <-->|Point Balance Check & Lock| Yotpo[Yotpo Loyalty SaaS]