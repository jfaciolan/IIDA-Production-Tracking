# Turmeric/Ginger Powder Production Tracking Technical Requirements (Version 1.0)

**Document Version:** 1.0
**Effective Date:** _______________  
**Author:** John Rey Faciolan
**Position:** Analytics and Automation Consultant
**Approved By:** _______________

---

## 1. PURPOSE

This document defines the complete technical requirements for the turmeric/ginger powder production tracking system, providing:

- Complete field-level mapping between paper documents and database tables
- Data validation rules and business logic specifications
- Key Performance Indicator calculation formulas
- System integration requirements
- Traceability and audit requirements

**Source Document Alignment:**
- All requirements derived from WHSE-01, WHSE-02, WHSE-03, WHSE-04 paper ledgers
- All requirements derived from DT-01 paper log
- All requirements derived from Production Ledgers (Excel sheets)
- All requirements derived from Production Traceability Document
- Complete field-to-field mapping provided

---

## 2. TERMS & DEFINITIONS

| Term | Definition | Source Document |
|------|------------|----------------|
| **Seq No.** | Sequential number for ledger entries | All WHSE ledgers Column A |
| **Endorsed Datetime** | Date and time when transaction was authorized | WHSE-01, 02, 03 Column B |
| **Received By** | Person who physically received materials | WHSE-01, 02, 03, 04 Column C |
| **Endorsed By** | Person who authorized the transaction | WHSE-01, 02, 03, 04 Column D |
| **Raw Mat Lot Number** | Raw Material Lot Identifier | WHSE-01, 02, 03, 04 Column J |
| **Downtime Code** | Standardized downtime category | DT-01 Column T |
| **Production Lot No.** | Production Batch Identifier | Travel Sheet Section 1 |
| **Target SKU** | Stock Keeping Unit being produced | Travel Sheet Section 1 |

---

## 3. SYSTEM ARCHITECTURE OVERVIEW

### 3.1 Document Flow Architecture

### 3.2 Data Consistency Requirements

**Primary Source Documents:**
1. **WHSE-01** - Primary source for raw material receipt data
2. **WHSE-02** - Primary source for returned material data
3. **WHSE-03** - Primary source for material issuance data
4. **WHSE-04** - Primary source for disposal data
5. **DT-01** - Primary source for downtime data
6. **Production Ledgers (Excel)** - Primary source for production process data
7. **Production Traceability Document** - Consolidated production record

**Cross-Reference Requirements:**
- WHSE-02 must reference valid WHSE-03 entries
- WHSE-03 must reference valid WHSE-01 entries
- WHSE-04 must reference valid WHSE-01 and/or WHSE-02 entries
- Production Ledgers must reference valid WHSE-03 entries for material usage

---

## 4. FIELD-LEVEL TECHNICAL SPECIFICATIONS

### 4.1 WHSE-01: Raw Material Receiving Ledger

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Seq No. | Integer | N/A | Yes | Auto-increment, unique | Column A |
| Endorsed Datetime | DateTime | N/A | Yes | Must be valid date/time | Column B |
| Received By | String | 50 | Yes | Must be valid employee ID | Column C |
| Endorsed By | String | 50 | Yes | Must be valid employee ID | Column D |
| Is Farmer | Boolean | N/A | Yes | True/False only | Column E |
| Raw Mat Name | String | 50 | Yes | "Turmeric" or "Ginger" only | Column F |
| Material Code | String | 10 | Yes | Must match Raw Mat Name | Column G |
| Qty | Decimal | 10,2 | Yes | Must be > 0 | Column H |
| Unit | String | 10 | Yes | kg, g, lb only | Column I |
| Raw Mat Lot Number | String | 20 | Yes | Must be unique | Column J |
| Unit Price | Decimal | 10,2 | Yes | Must be >= 0 | Column K |
| Farmer Paid | Boolean | N/A | No | Required if Is Farmer = True | Column L |
| Amount Paid | Decimal | 10,2 | No | Required if Farmer Paid = True | Column M |
| Paid By | String | 50 | No | Required if Amount Paid > 0 | Column N |

**Business Rules:**
1. `Raw Mat Lot Number` must follow format: `R<MATERIAL_CODE><DATE_CODE>`
2. `Material Code` must be consistent with `Raw Mat Name`
3. `Farmer Paid` can only be True if `Is Farmer` is True
4. `Amount Paid` must be provided if `Farmer Paid` is True
5. `Paid By` must be provided if `Amount Paid` is provided

### 4.2 WHSE-02: Returned Raw Material Ledger

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Seq No. | Integer | N/A | Yes | Auto-increment, unique | Column A |
| Endorsed Datetime | DateTime | N/A | Yes | Must be valid date/time | Column B |
| Received By | String | 50 | Yes | Must be valid employee ID | Column C |
| Endorsed By | String | 50 | Yes | Must be valid employee ID | Column D |
| Source Station | String | 50 | Yes | Valid production station name | Column E |
| Raw Mat Name | String | 50 | Yes | Must match WHSE-01 entry | Column F |
| Material Code | String | 10 | Yes | Must match WHSE-01 entry | Column G |
| Qty | Decimal | 10,2 | Yes | Must be > 0 | Column H |
| Unit | String | 10 | Yes | Must match WHSE-01 entry | Column I |
| Raw Mat Lot Number | String | 20 | Yes | Must exist in WHSE-01 | Column J |
| Storage Location | String | 50 | Yes | Valid storage location code | Column K |
| Returned to Prod. | Boolean | N/A | Yes | True/False only | Column L |
| Returned Datetime | DateTime | N/A | Yes | Must be <= Endorsed Datetime | Column M |
| Issuance Seq No. | Integer | N/A | Yes | Must exist in WHSE-03 | Column N |

**Business Rules:**
1. `Raw Mat Lot Number` must exist in WHSE-01
2. `Issuance Seq No.` must reference a valid WHSE-03 entry for same Raw Mat Lot Number
3. `Returned Datetime` must be after the WHSE-03 issuance datetime
4. `Qty` cannot exceed the issued quantity minus previously returned quantity
5. `Source Station` must match the WHSE-03 Target Station

### 4.3 WHSE-03: Raw Material Issuance Ledger

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Seq No. | Integer | N/A | Yes | Auto-increment, unique | Column A |
| Endorsed Datetime | DateTime | N/A | Yes | Must be valid date/time | Column B |
| Received By | String | 50 | Yes | Must be valid employee ID | Column C |
| Endorsed By | String | 50 | Yes | Must be valid employee ID | Column D |
| Target Station | String | 50 | Yes | Valid production station name | Column E |
| Raw Mat Name | String | 50 | Yes | Must match WHSE-01 entry | Column F |
| Material Code | String | 10 | Yes | Must match WHSE-01 entry | Column G |
| Qty | Decimal | 10,2 | Yes | Must be > 0 | Column H |
| Unit | String | 10 | Yes | Must match WHSE-01 entry | Column I |
| Raw Mat Lot Number | String | 20 | Yes | Must exist in WHSE-01 | Column J |
| Storage Location | String | 50 | Yes | Valid storage location code | Column K |
| is Fresh Raw Mat? | Boolean | N/A | Yes | True/False only | Column L |
| Returned Mat Seq | Integer | N/A | No | Must exist in WHSE-02 if provided | Column M |
| Orig Raw Mat Seq | Integer | N/A | Yes | Must exist in WHSE-01 | Column N |

**Business Rules:**
1. `Raw Mat Lot Number` must exist in WHSE-01
2. `Orig Raw Mat Seq` must reference the same Raw Mat Lot Number in WHSE-01
3. `is Fresh Raw Mat?` must be False if `Returned Mat Seq` is provided
4. `Returned Mat Seq` must reference a WHSE-02 entry with `Returned to Prod.` = True
5. Total issued quantity cannot exceed received quantity minus disposed quantity

### 4.4 WHSE-04: Raw Material Disposal Ledger

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Seq No. | Integer | N/A | Yes | Auto-increment, unique | Column A |
| Disposal Datetime | DateTime | N/A | Yes | Must be valid date/time | Column B |
| Received By | String | 50 | Yes | Must be valid employee ID | Column C |
| Endorsed By | String | 50 | Yes | Must be valid employee ID | Column D |
| Method | String | 20 | Yes | Composting, Landfill, Recycling, Special | Column E |
| Raw Mat Name | String | 50 | Yes | Must match WHSE-01 entry | Column F |
| Material Code | String | 10 | Yes | Must match WHSE-01 entry | Column G |
| Qty | Decimal | 10,2 | Yes | Must be > 0 | Column H |
| Unit | String | 10 | Yes | Must match WHSE-01 entry | Column I |
| Raw Mat Lot Number | String | 20 | Yes | Must exist in WHSE-01 | Column J |
| Storage Location | String | 50 | Yes | Valid storage location code | Column K |
| is Fresh Raw Mat? | Boolean | N/A | Yes | True/False only | Column L |
| Returned Mat Seq | Integer | N/A | No | Must exist in WHSE-02 if provided | Column M |
| Orig Raw Mat Seq | Integer | N/A | Yes | Must exist in WHSE-01 | Column N |

**Business Rules:**
1. `Raw Mat Lot Number` must exist in WHSE-01
2. `Orig Raw Mat Seq` must reference the same Raw Mat Lot Number in WHSE-01
3. `is Fresh Raw Mat?` must be False if `Returned Mat Seq` is provided
4. `Returned Mat Seq` must reference a valid WHSE-02 entry
5. Disposal quantity cannot exceed available quantity (received - issued + returned - previously disposed)

### 4.5 DT-01: Process Downtime Stop/Resume Log

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Seq No. | Integer | N/A | Yes | Auto-increment, unique | Column O |
| Station | String | 50 | Yes | Valid production station name | Column P |
| PIC | String | 50 | Yes | Must be valid employee ID | Column Q |
| Stop Datetime | DateTime | N/A | Yes | Must be valid date/time | Column R |
| Resumption Datetime | DateTime | N/A | Yes | Must be > Stop Datetime | Column S |
| Reason | String | 255 | Yes | Description of downtime cause | Column T |
| Downtime Code | String | 2 | Yes | PO, MP, NP, PI, O only | Column U |
| Downtime Duration (Hrs) | Decimal | 5,2 | Yes | Auto-calculated in hours | Column V |

**Business Rules:**
1. `Resumption Datetime` must be later than `Stop Datetime`
2. `Downtime Code` must be one of: PO, MP, NP, PI, O
3. `Downtime Duration (Hrs)` = (`Resumption Datetime` - `Stop Datetime`) in hours
4. Cannot have overlapping downtime entries for same station
5. `Station` must be a valid production station from Production Ledgers

### 4.6 Production Ledgers (Excel Sheets)

#### 4.6.1 01_Planning Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Format: P<PRODUCT_CODE><DATE_CODE> | Column A |
| Target SKU | String | 20 | Yes | Valid SKU code | Column B |
| Unit Weight | Decimal | 10,2 | Yes | Must be > 0 | Column C |
| Unit Price | Decimal | 10,2 | Yes | Must be >= 0 | Column D |
| Target Quantitiy | Decimal | 10,2 | Yes | Must be > 0 | Column E |
| Planned Start Date | Date | N/A | Yes | Must be valid date | Column F |
| Planned Start Time | Time | N/A | Yes | Must be valid time | Column G |
| Planned End Date | Date | N/A | Yes | Must be valid date | Column H |
| Planned End Time | Time | N/A | Yes | Must be valid time | Column I |
| Actual Quantitiy | Decimal | 10,2 | No | Must be >= 0 | Column J |
| Actual Start Datetime | DateTime | N/A | No | Must be valid date/time | Column K |
| Actual End Datetime | DateTime | N/A | No | Must be > Actual Start | Column L |

#### 4.6.2 02_Ingredient_Prep Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Must exist in 01_Planning | Column A |
| Start Date | Date | N/A | Yes | Must be valid date | Column B |
| Start Time | Time | N/A | Yes | Must be valid time | Column C |
| End Date | Date | N/A | Yes | Must be valid date | Column D |
| End Time | Time | N/A | Yes | Must be valid time | Column E |
| Raw Mat Lot | String | 20 | Yes | Must exist in WHSE-03 | Column F |
| Ingredient Lot | String | 20 | Yes | Format: I<MATERIAL_CODE><DATE_CODE> | Column G |
| Qty | Decimal | 10,2 | Yes | Must be > 0 | Column H |
| Unit | String | 10 | Yes | Must match WHSE-03 unit | Column I |
| PIC | String | 50 | Yes | Must be valid employee ID | Column J |

#### 4.6.3 03_Washing Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Must exist in 01_Planning | Column A |
| Ingredient Lot | String | 20 | Yes | Must exist in 02_Ingredient_Prep | Column B |
| Start Date | Date | N/A | Yes | Must be valid date | Column C |
| Start Time | Time | N/A | Yes | Must be valid time | Column D |
| End Date | Date | N/A | Yes | Must be valid date | Column E |
| End Time | Time | N/A | Yes | Must be valid time | Column F |
| Input Qty | Decimal | 10,2 | Yes | Must be > 0 | Column G |
| Output Qty | Decimal | 10,2 | Yes | Must be >= 0 | Column H |
| Unit | String | 10 | Yes | Must be consistent | Column I |
| PIC | String | 50 | Yes | Must be valid employee ID | Column J |

#### 4.6.4 04_Juicing Sheet

**Technical Requirements:** Same as 03_Washing sheet structure

#### 4.6.5 05_Cooking Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Must exist in 01_Planning | Column A |
| Start Date | Date | N/A | Yes | Must be valid date | Column B |
| Start Time | Time | N/A | Yes | Must be valid time | Column C |
| End Date | Date | N/A | Yes | Must be valid date | Column D |
| End Time | Time | N/A | Yes | Must be valid time | Column E |
| Total Input Qty | Decimal | 10,2 | Yes | Must be > 0 | Column F |
| Total Output Qty | Decimal | 10,2 | Yes | Must be >= 0 | Column G |
| Unit | String | 10 | Yes | Must be consistent | Column H |
| PIC | String | 50 | Yes | Must be valid employee ID | Column I |

#### 4.6.6 06_Drying Sheet
#### 4.6.7 07_Pulverizing Sheet
#### 4.6.8 08_Sieving Sheet

**Technical Requirements:** Same as 05_Cooking sheet structure

#### 4.6.9 09_Packaging Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Must exist in 01_Planning | Column A |
| Start Date | Date | N/A | Yes | Must be valid date | Column B |
| Start Time | Time | N/A | Yes | Must be valid time | Column C |
| End Date | Date | N/A | Yes | Must be valid date | Column D |
| End Time | Time | N/A | Yes | Must be valid time | Column E |
| Qty Packed | Decimal | 10,2 | Yes | Must be > 0 | Column F |
| PIC | String | 50 | Yes | Must be valid employee ID | Column G |

#### 4.6.10 10_Inspection Sheet

**Technical Requirements:**

| Field | Data Type | Length | Required | Validation Rules | Source |
|-------|-----------|--------|----------|-----------------|--------|
| Production Lot No. | String | 20 | Yes | Must exist in 01_Planning | Column A |
| Start Date | Date | N/A | Yes | Must be valid date | Column B |
| Start Time | Time | N/A | Yes | Must be valid time | Column C |
| End Date | Date | N/A | Yes | Must be valid date | Column D |
| End Time | Time | N/A | Yes | Must be valid time | Column E |
| Qty Inspected | Decimal | 10,2 | Yes | Must be > 0 | Column F |
| Qty Defective | Decimal | 10,2 | Yes | Must be >= 0 | Column G |
| PIC | String | 50 | Yes | Must be valid employee ID | Column H |

### 4.7 Production Traceability Document

**Technical Requirements:** (Maps to Production Ledgers Excel sheets)

#### Section 1: Production Planning
- Maps to 01_Planning sheet
- Includes both planned and actual production details
- Date Code creation guide: `<YEAR_END_CODE><MONTH_CODE><DATE>`
- Example: January 26, 2026 = F126

#### Section 2: Ingredient Preparation
- Maps to 02_Ingredient_Prep sheet
- Raw Mat Lot references WHSE-03
- Ingredient Lot format: `I<MATERIAL_CODE><DATE_CODE>`

#### Section 3: Process Steps
- Washing: Maps to 03_Washing sheet
- Juicing: Maps to 04_Juicing sheet
- Cooking through Sieving: Maps to 05_Cooking through 08_Sieving sheets
- Batch-based recording for Cooking, Drying, Pulverizing, Sieving

#### Section 4: Packaging & Quality Verification
- Packaging: Maps to 09_Packaging sheet
- Quality Verification: Maps to 10_Inspection sheet with additional inspection point details

---

## 5. DATA INTEGRITY REQUIREMENTS

### 5.1 Cross-Document Validation Rules

**Material Traceability Chain:**
1. Every WHSE-02 entry must reference a valid WHSE-03 entry
2. Every WHSE-03 entry must reference a valid WHSE-01 entry
3. Every WHSE-04 entry must reference a valid WHSE-01 entry
4. Every Production Ledgers "Raw Mat Lot" must reference a valid WHSE-03 entry

**Quantity Consistency Rules:**
1. Total issued (WHSE-03) ≤ Total received (WHSE-01) - Total disposed (WHSE-04)
2. Total returned (WHSE-02) ≤ Total issued (WHSE-03) for each Raw Mat Lot
3. Total disposed (WHSE-04) ≤ Total received (WHSE-01) - Total issued (WHSE-03) + Total returned (WHSE-02)

**Temporal Consistency Rules:**
1. WHSE-02 Returned Datetime must be after WHSE-03 Endorsed Datetime
2. WHSE-04 Disposal Datetime must be after WHSE-01 Endorsed Datetime
3. Production Ledgers Actual Start Datetime must be after WHSE-03 Endorsed Datetime
4. DT-01 Resumption Datetime must be after Stop Datetime

### 5.2 Business Logic Requirements

**Material Status Calculation:**
- Material is "RECEIVED" when recorded in WHSE-01
- Material is "ISSUED" when recorded in WHSE-03
- Material is "RETURNED" when recorded in WHSE-02 with Returned to Prod. = True
- Material is "DISPOSED" when recorded in WHSE-04
- Material cannot transition directly from RECEIVED to DISPOSED without ISSUANCE

**FIFO (First-In-First-Out) Implementation:**
1. System must track receipt date from WHSE-01 Endorsed Datetime
2. When issuing materials, system must prioritize by:
   - RETURNED materials (WHSE-02) first
   - Then oldest RECEIVED materials (WHSE-01)
   - Then newest RECEIVED materials
3. This logic must be enforced at data entry validation

**Disposal Authorization:**
1. Disposal requires supervisor approval (Endorsed By in WHSE-04)
2. Different disposal methods require different documentation:
   - Composting: No additional requirements
   - Landfill: Disposal receipt required
   - Recycling: Recycling log required
   - Special: Safety documentation required

---

## 6. KEY PERFORMANCE INDICATOR SPECIFICATIONS

### 6.1 Calculation Formulas

**1. Quality Performance:**
- **Formula:** (Total Defective Units ÷ Total Inspected Units) × 100
- **Data Sources:** 10_Inspection sheet
- **Calculation Frequency:** Per production lot, daily, monthly
- **Target:** ≤ 3%

**2. Financial Losses Due to Quality:**
- **Formula:** (Defective Units × Unit Price) + (Downtime Hours × Hourly Operating Cost)
- **Data Sources:** 10_Inspection + DT-01 + 01_Planning Unit Price
- **Calculation Frequency:** Per production lot
- **Target:** Minimize

**3. Production Yield:**
- **Formula:** (Actual Quantity ÷ Target Quantity) × 100
- **Data Sources:** 01_Planning sheet
- **Calculation Frequency:** Per production lot
- **Target:** ≥ 95%

**4. Process Efficiency (Weight-based):**
- **Formula:** (Output Qty ÷ Input Qty) × 100
- **Data Sources:** Process sheets (03-08)
- **Calculation Frequency:** Per batch, per station
- **Target:** Process-specific targets

**5. Process Efficiency (Piece-based):**
- **Formula:** (Pieces Produced ÷ Standard Pieces per Hour) × 100
- **Data Sources:** 09_Packaging sheet
- **Calculation Frequency:** Per packaging batch
- **Target:** ≥ 90%

**6. Raw Material Utilization:**
- **Formula:** (Total Issued Qty ÷ Total Received Qty) × 100
- **Data Sources:** WHSE-01, WHSE-03
- **Calculation Frequency:** Daily, monthly, per material type
- **Target:** ≥ 90%

**7. Return Rate:**
- **Formula:** (Returned Qty ÷ Issued Qty) × 100
- **Data Sources:** WHSE-02, WHSE-03
- **Calculation Frequency:** Daily, monthly
- **Target:** ≤ 10%

**8. Disposal Rate:**
- **Formula:** (Disposed Qty ÷ Received Qty) × 100
- **Data Sources:** WHSE-04, WHSE-01
- **Calculation Frequency:** Daily, monthly
- **Target:** ≤ 5%

**9. Downtime Percentage:**
- **Formula:** (Total Downtime Hours ÷ Total Production Hours) × 100
- **Data Sources:** DT-01, 01_Planning sheet
- **Calculation Frequency:** Daily, weekly
- **Target:** ≤ 5%

### 6.2 KPI Reporting Requirements

**Daily Reports:**
1. Production Summary Report
   - Production lots completed
   - Total output quantity
   - Quality performance
   - Downtime summary
2. Material Utilization Report
   - Raw material received
   - Raw material issued
   - Returns and disposals
   - Utilization rates

**Weekly Reports:**
1. KPI Trend Analysis
   - Week-over-week comparisons
   - Target vs. actual performance
   - Exception reporting
2. Quality Performance Report
   - Defect analysis by type
   - Rejection rate trends
   - Cost of quality

**Monthly Reports:**
1. Financial Impact Report
   - Total material costs
   - Quality loss costs
   - Disposal costs
   - Downtime costs
2. Compliance Report
   - Document completion rates
   - Traceability compliance
   - SOP adherence

---

## 7. SYSTEM INTEGRATION REQUIREMENTS

### 7.1 Data Capture Requirements

**Paper-to-Digital Conversion:**
1. All WHSE-01 through WHSE-04 entries must be digitized within 24 hours
2. All DT-01 entries must be digitized within 4 hours of resumption
3. Production Ledgers data must be digitized within 24 hours of production completion
4. Data entry must include validation against paper source documents

**Validation Requirements:**
1. Cross-document references must be validated during data entry
2. Quantity consistency must be validated in real-time
3. Temporal consistency must be validated
4. Employee ID validation must be performed

### 7.2 Reporting Requirements

**Real-time Dashboards:**
1. Production Status Dashboard
   - Current production lots
   - Active processes
   - Real-time KPIs
2. Material Inventory Dashboard
   - Current inventory levels
   - Material movement trends
   - Utilization rates

**Scheduled Reports:**
1. Daily production reports by 9:00 AM next day
2. Weekly KPI reports by Monday 10:00 AM
3. Monthly compliance reports by 5th of following month

**Ad-hoc Reporting:**
1. Traceability reports for any production lot
2. Material movement history for any raw material lot
3. Quality performance analysis by date range

### 7.3 Audit Requirements

**Traceability Reports:**
For any Production Lot Number, system must generate report showing:
1. Raw material sources (WHSE-01 references)
2. Material issuance records (WHSE-03 references)
3. Processing steps (Production Ledgers)
4. Quality inspection results (10_Inspection sheet)
5. Any returns (WHSE-02 references)
6. Any disposals (WHSE-04 references)
7. Any downtime (DT-01 references)

**Compliance Reports:**
1. Document completion rates by department
2. Data entry timeliness reports
3. Validation error reports
4. SOP adherence metrics

---

## 8. DATA RETENTION AND ARCHIVAL

### 8.1 Retention Periods

**Paper Documents:**
- WHSE-01 through WHSE-04: 2 years minimum
- DT-01: 2 years minimum
- Production Ledgers: 2 years minimum
- Production Traceability Document: 2 years minimum
- After 2 years: Scan and archive digitally, destroy originals after verification

**Digital Records:**
- Transaction records: 7 years minimum
- KPI data: 10 years minimum
- Audit trails: Permanent

### 8.2 Archival Requirements

**Paper Archival:**
1. Organized by document type and date
2. Cross-referenced by Production Lot Number
3. Stored in climate-controlled environment
4. Indexed for quick retrieval

**Digital Archival:**
1. Daily backups of all transaction data
2. Weekly full system backups
3. Monthly archival to offline storage
4. Yearly verification of backup integrity

---

## 9. SECURITY AND ACCESS CONTROL

### 9.1 Access Levels

**Warehouse Staff:**
- Create and view WHSE-01, WHSE-02, WHSE-03, WHSE-04 entries
- Cannot modify or delete entries after endorsement
- View material inventory reports

**Production Staff:**
- Create and view Production Ledgers entries
- Create and view DT-01 entries
- View production schedules
- Cannot modify entries after shift completion

**Supervisors:**
- Endorse transactions in all documents
- View all reports
- Modify entries with approval trail
- Generate KPI reports

**Management:**
- Full system access
- Configuration privileges
- Audit trail access
- Financial reporting access

### 9.2 Audit Trail Requirements

**All transactions must record:**
1. User ID of person creating entry
2. Date and time of creation
3. User ID of person endorsing entry
4. Date and time of endorsement
5. Any modifications with before/after values
6. Reason for modification

**Audit trail must be:**
1. Immutable once recorded
2. Accessible only to authorized personnel
3. Retained for minimum of 7 years
4. Searchable by date, user, document type, and transaction type

---

## 10. IMPLEMENTATION REQUIREMENTS

### 10.1 Phase 1: Document Alignment

**Requirements:**
1. Map all paper fields to database fields exactly as named
2. Implement data validation rules per Section 4
3. Create data entry interfaces matching paper forms
4. Implement cross-document validation

**Deliverables:**
1. Complete field mapping document
2. Data entry application with validation
3. Basic reporting framework
4. User training materials

### 10.2 Phase 2: KPI Implementation

**Requirements:**
1. Implement all KPI calculations per Section 6
2. Create real-time dashboards
3. Implement scheduled reporting
4. Create exception alerting system

**Deliverables:**
1. KPI calculation engine
2. Management dashboards
3. Automated reporting system
4. Alert notification system

### 10.3 Phase 3: Advanced Features

**Requirements:**
1. Implement traceability reporting
2. Create audit trail system
3. Implement compliance monitoring
4. Create predictive analytics

**Deliverables:**
1. Complete traceability reporting
2. Audit and compliance system
3. Advanced analytics module
4. Mobile access capabilities

---

## APPENDIX A: DOCUMENT FIELD MAPPING MATRIX

### A.1 WHSE Series Ledgers Common Fields

| Field Name | WHSE-01 | WHSE-02 | WHSE-03 | WHSE-04 | Data Type | Validation |
|------------|---------|---------|---------|---------|-----------|------------|
| Seq No. | Col A | Col A | Col A | Col A | Integer | Auto-increment |
| [Date]Time | Col B | Col B | Col B | Col B | DateTime | Valid date/time |
| Received By | Col C | Col C | Col C | Col C | String(50) | Valid employee |
| Endorsed By | Col D | Col D | Col D | Col D | String(50) | Valid employee |
| Raw Mat Name | Col F | Col F | Col F | Col F | String(50) | Turmeric/Ginger |
| Material Code | Col G | Col G | Col G | Col G | String(10) | TUR/GIN |
| Qty | Col H | Col H | Col H | Col H | Decimal(10,2) | > 0 |
| Unit | Col I | Col I | Col I | Col I | String(10) | kg/g/lb |
| Raw Mat Lot Number | Col J | Col J | Col J | Col J | String(20) | Unique, valid format |
| Storage Location | N/A | Col K | Col K | Col K | String(50) | Valid location |

### A.2 Production Ledgers Field Groups

| Sheet | Field Group | Fields | Data Type | Validation |
|-------|-------------|--------|-----------|------------|
| 01_Planning | Production Planning | 12 fields | Mixed | See Section 4.6.1 |
| 02_Ingredient_Prep | Ingredient Preparation | 10 fields per row | Mixed | See Section 4.6.2 |
| 03_Washing | Process Step | 10 fields per row | Mixed | See Section 4.6.3 |
| 04_Juicing | Process Step | 10 fields per row | Mixed | See Section 4.6.4 |
| 05_Cooking | Process Step | 9 fields per row | Mixed | See Section 4.6.5 |
| 06_Drying | Process Step | 9 fields per row | Mixed | See Section 4.6.6 |
| 07_Pulverizing | Process Step | 9 fields per row | Mixed | See Section 4.6.7 |
| 08_Sieving | Process Step | 9 fields per row | Mixed | See Section 4.6.8 |
| 09_Packaging | Packaging | 7 fields per row | Mixed | See Section 4.6.9 |
| 10_Inspection | Quality Verification | 8 fields per row | Mixed | See Section 4.6.10 |

### A.3 Cross-Reference Matrix

| Source Document | Target Document | Reference Field | Validation Required |
|----------------|----------------|----------------|---------------------|
| WHSE-02 | WHSE-03 | Issuance Seq No. | Must exist in WHSE-03 |
| WHSE-03 | WHSE-01 | Orig Raw Mat Seq | Must exist in WHSE-01 |
| WHSE-04 | WHSE-01 | Orig Raw Mat Seq | Must exist in WHSE-01 |
| WHSE-04 | WHSE-02 | Returned Mat Seq | Must exist in WHSE-02 (if provided) |
| Production Ledgers | WHSE-03 | Raw Mat Lot | Must exist in WHSE-03 |
| Production Ledgers | Production Ledgers | Production Lot No. | Must exist in 01_Planning |
| 02_Ingredient_Prep | 03-08 Sheets | Ingredient Lot | Must exist in 02_Ingredient_Prep |

---

**DOCUMENT END**

**REVISION HISTORY**

| Version | Date | Author | Changes |
| ------- | ---- | ------ | ------- |
| 1.0 | Initial | Technical Team | Initial release aligned with all source documents |

**APPROVAL SIGNATURES**

Technical Lead: _______________  
Date: _______________

Production Manager: _______________  
Date: _______________

Quality Assurance: _______________  
Date: _______________