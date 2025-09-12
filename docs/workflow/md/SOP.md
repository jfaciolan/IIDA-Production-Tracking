# IIDA Farms – Standard Operating Procedure (SOP) for Turmeric/Ginger Powder Production & Data Recording

* * *

## 1\. Purpose

This SOP provides step-by-step instructions for warehouse staff, operators, and supervisors to ensure consistent production and accurate data recording across all stages of turmeric/ginger powder production. It is designed to complement the technical specification and support real-time monitoring, traceability, and KPI reporting.

* * *

## 2\. Scope

This SOP applies to all staff handling raw material receiving, production processes, packaging, inventory movements, and data recording in the IIDA Farms turmeric/ginger powder value chain.

* * *

## 3\. Roles & Responsibilities

- **Warehouse Staff:** Receive and record raw materials, procurement source, quantities, and prices. Manage returned raw materials separately from fresh receipts.
- **Finance:** Confirm payments, verify unit prices.
- **Supervisors:** Define production lots, planned vs actual, monitor timelines, sign off.
- **Operators:** Execute and record all process steps, including stoppages, inputs/outputs, and packaging.
- **QC Staff:** Validate yields, losses, packaging accuracy.

* * *

## 4\. Procedures

### 4.1 Raw Material Receiving (Warehouse)

1.  Record **Procurement Source**: Farmer, Market, Other.
    
    - If Farmer → record Farmer Name + Payment status.
    - If Market/Other → record description.
2.  Generate **Raw Material Lot Number** (auto by system).
    
3.  Record **Produce Name** (Turmeric/Ginger).
    
4.  Enter **Quantity** and **Unit** (g, kg, ml, l).
    
5.  Enter **Unit Price** (currency).
    
6.  Finance updates **Farmer Paid** status.
    

**Mermaid Workflow (Fresh Receipt):**

```mermaid
graph TD
  A1[Receive Raw Material] --> A2[Record Procurement Source]
  A2 --> A3[Assign Raw Mat Lot Number]
  A3 --> A4[Record Produce, Qty, Unit]
  A4 --> A5[Record Unit Price]
  A5 --> A6[Finance Confirms Payment]
```

* * *

### 4.1.1 Handling Returned Raw Materials (Warehouse)

Returned materials from the production area must be clearly identified and separated from freshly received warehouse stock.

1.  When raw material is returned from **Ingredient Preparation** or other production processes:
    
    - Record **Returned Qty**.
    - Record **Return Reason** (Damaged, Excess, Wrong, Quality, Other).
    - Identify as **Returned from Production** (flag in system).
    - Preserve **original Raw Material Lot Number** (no new lot created).
2.  Move the returned material back to warehouse storage.
    
3.  Warehouse inventory must track separately:
    
    - **Fresh Receipts** (never issued to production).
    - **Returned from Production** (previously issued, now returned).
4.  When issuing raw mats for production, system enforces **FIFO priority**:
    
    - Returned items first.
    - Then oldest fresh receipts.
    - Finally newest fresh receipts.

**Mermaid Workflow (Returned Materials):**

```mermaid
graph TD
  RA1[Raw Mat Returned from Production] --> RA2[Record Returned Qty + Reason]
  RA2 --> RA3[Flag as Returned from Production]
  RA3 --> RA4[Preserve Original Lot Number]
  RA4 --> RA5[Move Back to Storage]
  RA5 --> RA6[Update Warehouse Inventory]
  RA6 --> RA7[FIFO Priority: Returned > Oldest Fresh > Newest Fresh]
```

* * *

### 4.2 Production Initiation (Supervisor)

1.  Assign **Lot Number** (`YYYYMMDD-SEQ-PRODUCT`).
2.  Enter **Product Name** and **Planned Quantity**.
3.  Record **Start DateTime**.
4.  At end, record **Actual Quantity** and **End DateTime**.

**Mermaid Workflow:**

```mermaid
graph TD
  B1[Supervisor Opens Lot] --> B2[Assign Lot Number]
  B2 --> B3[Enter Product + Planned Qty]
  B3 --> B4[Start DateTime]
  B4 --> B5[End of Run: Actual Qty + End DateTime]
```

* * *

### 4.3 Ingredient Preparation (Operator)

Ingredient preparation ensures raw materials are issued to production in FIFO (First-In, First-Out) order to maintain quality. Returned raw materials are prioritized first before issuing new materials.

#### Normal Flow (Issuing to Production)

1.  Pull raw material from warehouse, following **FIFO order**.
2.  Enter **Raw Material Lot Number** (no new lot is generated).
3.  Record **Issued Qty + Unit**.
4.  Endorse directly to production with existing lot number.
5.  Always record **PIC**.

**Mermaid Workflow (Normal):**

```mermaid
graph TD
  C1[Warehouse Inventory] --> C2[Select Next Lot in FIFO]
  C2 --> C3[Enter Raw Mat Lot Number]
  C3 --> C4[Record Issued Qty]
  C4 --> C5[Endorse to Production]
  C5 --> C6[Record PIC]
```

#### Flow with Returned Material

1.  If material is returned during preparation:
    
    - Record **Returned Qty + Reason**.
    - Move the returned raw material back to **warehouse storage**.
    - Maintain its **original Raw Material Lot Number**.
2.  When endorsing to production:
    
    - Prioritize using **returned materials first**.
    - After returns are consumed, continue with FIFO order for remaining lots.

**Mermaid Workflow (Return Case):**

```mermaid
graph TD
  R1[Raw Material in Prep] --> R2{Return Needed?}
  R2 -- Yes --> R3[Record Returned Qty + Reason]
  R3 --> R4[Move Back to Warehouse Storage]
  R4 --> R5[Preserve Original Lot Number]
  R5 --> R6[Flag as Returned from Production]
  R6 --> R7[Prioritize Returned Material When Endorsing]
  R2 -- No --> R8[Proceed with Normal FIFO Endorsement]
```

* * *

### 4.4 Processing Steps (Multi-Ingredient Handling)

From Washing to Cooking, ingredients may be handled separately and then combined. After Cooking, all processes operate on a **single unified product lot**.

#### 4.4.1 Washing & Juicing (Per Ingredient Lot)

1.  At **start or resumption**, record **Input Qty per Raw Material Lot**.
    
2.  Record **Start DateTime**.
    
3.  If stoppage occurs:
    
    - Record **Stop DateTime** and **Reason**.
    - Record **Output Qty per Raw Material Lot** at stoppage.
    - Record **Resume DateTime**.
    - Record **new Input Qty** at resumption.
4.  At the **end of step**, record **final Output Qty per Raw Material Lot**.
    

**Mermaid Workflow (Washing/Juicing):**

```mermaid
graph TD
  W1[Input Raw Mat Lot 1] --> W3[Washing/Juicing]
  W2[Input Raw Mat Lot 2] --> W3
  W3 --> W4[Output per Lot Recorded]
```

#### 4.4.2 Cooking (Combination Step)

1.  Gather multiple ingredient lots (e.g., turmeric juice, ginger juice, calamansi, sugar).
    
2.  At **start or resumption**, record **Input Qty per Ingredient Lot**.
    
3.  Record **Start DateTime**.
    
4.  If stoppage occurs:
    
    - Record **Stop Datetime + Reason**.
    - Record **Partial Output Qty** at stoppage.
    - Record **Resume Datetime**.
    - Record **Input Qty per Ingredient Lot** at resumption.
5.  At **end of Cooking**, record **one combined Product Lot Number** with **Output Qty**.
    

**Mermaid Workflow (Cooking Combination):**

```mermaid
graph TD
  J1[Turmeric Juice Lot] --> K1[Cooking]
  J2[Ginger Juice Lot] --> K1
  J3[Calamansi Lot] --> K1
  J4[Sugar Lot] --> K1
  K1 --> K2[One Combined Product Lot Output]
```

#### 4.4.3 Post-Cooking (Drying → Sieving)

1.  Processes now handle **only one unified product lot**.
2.  At **start or resumption**, record **Input Qty**.
3.  If stoppage occurs: record **Stop Time, Reason, Output Qty**.
4.  At **resumption**, record **Input Qty**.
5.  At **end**, record **Output Qty**.

**Mermaid Workflow (Unified Lot Handling):**

```mermaid
graph TD
  L1[Unified Product Lot] --> L2[Drying]
  L2 --> L3[Pulverizing]
  L3 --> L4[Sieving]
  L4 --> L5[Packaging]
```

* * *

### 4.5 Packaging & Inventory (Operator)

1.  Select **Lot Number**.
2.  Choose **SKU Code** (dropdown). Example: `TGLM-PCH-500G`.
3.  Confirm product and SRP shown on form.
4.  Enter **Qty Packaged (units)**.
5.  At **start or resumption**, record **Input Qty**.
6.  At **end or stoppage**, record **Output Qty**.
7.  Record **Inventory Move** (To Warehouse, To Dispatch).
8.  Record **Start/Stop/Resume** if interrupted.
9.  Confirm **PIC**.

**Mermaid Workflow:**

```mermaid
graph TD
  E1[Select Lot] --> E2[Choose SKU Code]
  E2 --> E3[System Shows Product + SRP]
  E3 --> E4[Enter Qty Packaged]
  E4 --> E5[Record Input Qty at Start/Resume]
  E5 --> E6{Interrupted?}
  E6 -- Yes --> E7[Log Stop Time + Reason]
  E7 --> E8[Record Output Qty at Stop]
  E8 --> E9[Log Resume Time]
  E9 --> E10[Record Input Qty at Resumption]
  E6 -- No --> E11
  E10 --> E11
  E11 --> E12[At End: Record Final Output Qty]
  E12 --> E13[Inventory Move + PIC Confirms]
```

* * *

### 4.6 Stoppage & Resumption (All steps)

- At stoppage:
    
    - Record Stop Datetime
    - Record Stop Reason
    - Record **Output Qty at stoppage**
- At resumption:
    
    - Record Resume Datetime
    - Record **Input Qty at resumption**
    - PIC confirms

**Mermaid Workflow:**

```mermaid
graph TD
  F1[Process Interrupted] --> F2[Record Stop Time + Reason]
  F2 --> F3[Record Output Qty at Stoppage]
  F3 --> F4[Record Resume Time]
  F4 --> F5[Record Input Qty at Resumption]
  F5 --> F6[PIC Confirms]
```

* * *

### 4.7 KPI Recording

Staff entries drive system KPIs:

- **Yield % per Step:** `(Output ÷ Input) × 100`.
- **Loss % per Step:** `((Input – Output) ÷ Input) × 100`.
- **Planned vs Actual Timeline:** Supervisor’s planned vs actual times.
- **Downtime Frequency/Duration:** Derived from Stop/Resume logs.
- **Cost of Inputs:** Σ(`Raw Material Qty × Price`).
- **Cost of Outputs:** Σ(`Output Qty × SKU SRP`).

* * *

## 5\. Data Recording Rules

- Always record **Input Qty** at start or resumption.
- Always record **Output Qty** at stoppage and end of process.
- Record **in real time** whenever possible.
- If offline → use GSheet or logbook, update within 24h.
- Use only standard **units** (g, kg, ml, l).
- Use only official **SKU Codes**.
- Supervisors review & sign off daily.

* * *

## 6\. SKU Master List (Reference for Operators)

| SKU Code | Product | Size | SRP |
| --- | --- | --- | --- |
| TGLM-PCH-500G | Turmeric Ginger Lemongrass | 500g | P220 |
| TGLM-PCH-200G | Turmeric Ginger Lemongrass | 200g | P130 |
| TGLM-SAC-10GX12 | Turmeric Ginger Lemongrass | 10g×12 sachets | P115 |
| TJD-PCH-500G | Turmeric Juice Drink | 500g | P220 |
| TJD-PCH-200G | Turmeric Juice Drink | 200g | P130 |
| TJD-SAC-10GX12 | Turmeric Juice Drink | 10g×12 sachets | P115 |
| TJWC-PCH-500G | Turmeric Juice with Calamansi | 500g | P220 |
| TJWC-PCH-200G | Turmeric Juice with Calamansi | 200g | P130 |
| GTSB-PCH-500G | Ginger Tea (Salabat) | 500g | P220 |
| GTSB-PCH-200G | Ginger Tea (Salabat) | 200g | P130 |
| GTSB-SAC-10GX12 | Ginger Tea (Salabat) | 10g×12 sachets | P115 |
| TGLB-TBG-3.5GX10 | Turmeric Ginger Lemongrass Brew | 3.5g×10 | P130 |
| TGLB-CIN-TBG-3.5GX10 | TGLM Brew w/ Cinnamon | 3.5g×10 | P150 |
| TGPP-PCH-50G | Turmeric Ginger Pulp | 50g | P95 |

* * *

## 7\. Compliance & Audit

- All data entries are part of full production traceability.
- Missing or late entries will trigger dashboard alerts.
- Supervisors must audit records weekly for accuracy and completeness.

* * *