## \# System Boundary & Functional Use Case Specifications

## \*\*Project Title:\*\* E-Commerce Checkout & Retention Optimization  

## \*\*Target Domain:\*\* E-Commerce, System Boundaries, & Actor Interactions  

## \*\*Document Owner:\*\* Business Systems Analyst  

## \*\*Document Version:\*\* 1.0 (Final Baseline Specification)  

## \> \*\*Portfolio Context Note:\*\*  

## \> This document represents a self-directed Business Analysis Case Study designed to simulate end-to-end enterprise software requirements. Generative AI was utilized as an interactive sounding board to stress-test edge cases, simulate developer constraints, and validate non-functional compliance boundaries.

## \---

## \#\# 1\. System Boundary Overview

## The system boundary encompasses all checkout processing, customer profile identification, payment gateway interactions, cart state timers, dynamic shipping lookup handlers, and marketing/loyalty integration hooks. 

## Components outside the boundary include third-party API internal operations (Carrier algorithms, Payment Gateway clearing houses, Klaviyo campaign email render engines).

## \`\`\`mermaid

## graph TB

##     subgraph Inside System Boundary \["System Boundary: E-Commerce Checkout & Retention Engine"\]

##         UC1\[UC-01: Calculate Dynamic Shipping Rates\]

##         UC2\[UC-02: Process Guest Checkout & Payment\]

##         UC3\[UC-03: Trigger Abandoned Cart Recovery Payload\]

##         UC4\[UC-04: Redeem Loyalty Points at Checkout\]

##     end

## 

##     Guest\[Guest Shopper\] \--\>|Initiates| UC2

##     Registered\[Registered Shopper\] \--\>|Initiates| UC1

##     Registered \--\>|Initiates| UC4

##     

##     UC1 \<--\>|ZIP & Cart Weight| Carrier\[Carrier Shipping API\]

##     UC2 \<--\>|Payment Payload| Gateway\[Payment Gateway\]

##     UC2 \-- Includes \--\> UC1

##     UC2 \-. Extends on 2-hr Idle .-\> UC3

##     

##     UC3 \--\>|Emit Recovery Payload| Klaviyo\[Klaviyo Email SaaS\]

##     UC4 \<--\>|Point Balance Check & Lock| Yotpo\[Yotpo Loyalty SaaS\]

## \`\`\`

## **2\. Actor Classification Matrix**

## 

| Actor Name | Type | Interaction Level | Responsibilities in System |
| ----- | ----- | ----- | ----- |
| **Guest Shopper** | Primary | External (Human) | Initiates checkout, provides shipping address and payment details without registering an account. |
| **Registered Shopper** | Primary | External (Human) | Authenticates session, uses saved shipping details, earns loyalty points, and applies point discounts. |
| **Payment Gateway** | Secondary | External (System API) | Receives encrypted payment tokens, validates card credentials, and returns auth/decline responses. |
| **Carrier Shipping API** | Secondary | External (System API) | Accepts postal codes and item package weights; returns real-time dynamic shipping options. |
| **Klaviyo Email SaaS** | Secondary | External (System API) | Ingests JSON event payloads for abandoned cart sessions to execute automated recovery email flows. |
| **Yotpo Loyalty SaaS** | Secondary | External (System API) | Manages customer reward tiers, point ledger balances, and point redemption authorizations. |

## **3\. Use Case Inventory**

## 

| Use Case ID | Use Case Name | Primary Actor | Associated Business Rules |
| ----- | ----- | ----- | ----- |
| **UC-01** | Calculate Dynamic Shipping Rates | Registered / Guest Shopper | **BR-03** (3.0s Timeout Fallback) |
| **UC-02** | Process Guest Checkout & Payment | Guest Shopper | **BR-03**, **BR-04** (Frictionless Guest Checkout) |
| **UC-03** | Trigger Abandoned Cart Recovery Payload | Background Engine (System) | **BR-01** (120-min timer, $10 min, 7-day cap) |
| **UC-04** | Redeem Loyalty Points at Checkout | Registered Shopper | **BR-02**, **BR-02.1** ($30 min gate, 30-min hold) |

