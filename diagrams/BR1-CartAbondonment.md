{\rtf1\ansi\ansicpg1252\cocoartf2761
\cocoatextscaling0\cocoaplatform0{\fonttbl\f0\fswiss\fcharset0 Helvetica;}
{\colortbl;\red255\green255\blue255;}
{\*\expandedcolortbl;;}
\paperw11900\paperh16840\margl1440\margr1440\vieww28600\viewh15260\viewkind0
\pard\tx720\tx1440\tx2160\tx2880\tx3600\tx4320\tx5040\tx5760\tx6480\tx7200\tx7920\tx8640\pardirnatural\partightenfactor0

\f0\fs24 \cf0 ```mermaid\
graph TD\
    A([Initial Node: User Enters Email + Items in Cart]) --> B[Calculate Cart Subtotal]\
    B --> C\{Is Subtotal >= $10.00?\}\
    C -- No --> D([Activity Canceled])\
    C -- Yes --> E[Start 120-Min Idle Timer]\
    \
    E --> F\{User Activity Event?\}\
    F -- Order Completed --> G[Cancel Timer] --> H([Activity Canceled])\
    F -- Cart Emptied --> I[Cancel Timer] --> J([Activity Canceled])\
    F -- 120 Mins Elapsed --> K[Re-evaluate Cart Subtotal]\
    \
    K --> L\{Is Subtotal >= $10.00?\}\
    L -- No --> M([Activity Canceled])\
    L -- Yes --> N[Query Customer Email Log]\
    \
    N --> O\{Email Sent in Last 7 Days?\}\
    O -- Yes --> P[Log Suppression Event] --> Q([Activity Complete])\
    O -- No --> R[Emit Payload to Email SaaS] --> S[Log Sent Timestamp] --> T([Activity Complete])\
```}