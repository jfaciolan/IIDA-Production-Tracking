
## 1.  Production Initiation – Lot Creation (Supervisor)

| Time | Action (Supervisor) | **Form** | Form Field(s) Updated | PIC |
|------|---------------------|----------|-----------------------|-----|
| 07:45 | **Create** a new production lot for Turmeric‑Ginger Lemongrass. | `LOT_CREATION_FORM` (see “Lot Creation Guide”) | • `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `product_name`: *Turmeric Ginger Lemongrass*  <br>• `planned_qty`: *100.0* kg  <br>• `start_datetime`: *2024‑06‑15 09:00*  <br>• `supervisor_id`: *EMP‑SUP‑002* | Supervisor (EMP‑SUP‑002) |

> **Lot Creation** is the first step; it generates the unique `LOT‑Lot` ID that all subsequent forms will reference.

---

## 2.  Receiving Raw Material (Warehouse)

| Time | Action (Warehouse) | **Form** | Form Field(s) Updated | PIC |
|------|--------------------|----------|-----------------------|-----|
| 08:00 | **Receive** fresh turmeric from Farmer “Juan Dela Cruz”. | `RAW_RECEIPT_FORM` | • `raw_mat_lot`: **RM‑TUR‑20240615-0001**  <br>• `endorsement_date`: *2024‑06‑15*  <br>• `procurement_source`: **Farmer**  <br>• `farmer_name`: *Juan Dela Cruz*  <br>• `produce_name`: **Turmeric**  <br>• `qty`: *120.0* kg  <br>• `unit_id`: **kg**  <br>• `condition_before_endorsement`: **GOOD** | Warehouse Staff (EMP‑WHS‑001) |
| 08:05 | **Set status** to *Received* and add history entry. | `RAW_RECEIPT_FORM` | • `status`: **Received**  <br>• History: “2024‑06‑15 08:05 – Received → Received (Juan)” | Warehouse Staff (EMP‑WHS‑001) |

---

## 3.  Issuing to Ingredient Prep (Warehouse)

| Time | Action (Warehouse) | **Form** | Form Field(s) Updated | PIC |
|------|--------------------|----------|-----------------------|-----|
| 08:30 | **Create** issuance record for the lot. | `ISSUE_RECORD_FORM` | • `raw_mat_lot`: *RM‑TUR‑20240615-0001*  <br>• `qty`: *120.0* kg  <br>• `unit_id`: **kg**  <br>• `status`: **Issued** | Warehouse Staff (EMP‑WHS‑001) |
| 08:35 | **Add** status history. | `ISSUE_RECORD_FORM` | • History: “2024‑06‑15 08:35 – Received → Issued (Juan)” | Warehouse Staff (EMP‑WHS‑001) |

---

## 4.  Ingredient Preparation & Endorsement (Operator)

| Time | Action (Operator) | **Form** | Form Field(s) Updated | PIC |
|------|-------------------|----------|-----------------------|-----|
| 08:40 | **Generate** ingredient‑prep lot ID and endorse. | `INGREDIENT_PREP_FORM` | • `ingredient_lot`: **ING‑RM‑TUR‑20240615‑0001‑001**  <br>• `raw_mat_lot`: *RM‑TUR‑20240615-0001*  <br>• `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `qty`: *120.0* kg  <br>• `unit_id`: **kg**  <br>• `endorsement_datetime`: *2024‑06‑15 08:40*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 08:45 | **Transfer** to cooking station. | `INGREDIENT_PREP_FORM` (same) | • Status changed to *Endorsed*  <br>• History: “2024‑06‑15 08:45 – Issued → Endorsed (Operator)” | Operator (EMP‑OP‑003) |

---

## 5.  Cooking (Process Step)

| Time | Action (Operator) | **Form** | Form Field(s) Updated | PIC |
|------|-------------------|----------|-----------------------|-----|
| 09:00 | **Start** cooking for the lot. | `PROCESS_STEP_FORM` | • `process_id`: *C001*  <br>• `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `ingredient_lot`: *ING‑RM‑TUR‑20240615‑0001‑001*  <br>• `input_qty`: *120.0* kg  <br>• `start_datetime`: *2024‑06‑15 09:00*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 09:20 | **Stop** for maintenance. | `STOP_RESUME_LOG_FORM` | • `stop_datetime`: *2024‑06‑15 09:20*  <br>• `stop_reason_id`: *Maintenance – Routine Check*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 09:25 | **Resume** cooking. | `STOP_RESUME_LOG_FORM` | • `resume_datetime`: *2024‑06‑15 09:25*  <br>• `input_qty`: *120.0* kg | Operator (EMP‑OP‑003) |
| 10:00 | **Finish** cooking. | `PROCESS_STEP_FORM` | • `stop_datetime`: *2024‑06‑15 10:00*  <br>• `output_qty`: *112.0* kg | Operator (EMP‑OP‑003) |

---

## 6.  Drying (Process Step)

| Time | Action (Operator) | **Form** | Form Field(s) Updated | PIC |
|------|-------------------|----------|-----------------------|-----|
| 10:05 | **Start** drying. | `PROCESS_STEP_FORM` | • `process_id`: *D001*  <br>• `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `input_qty`: *112.0* kg  <br>• `start_datetime`: *2024‑06‑15 10:05*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 10:20 | **Stop** for power outage. | `STOP_RESUME_LOG_FORM` | • `stop_datetime`: *2024‑06‑15 10:20*  <br>• `stop_reason_id`: *Power Outage*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 10:25 | **Resume** drying. | `STOP_RESUME_LOG_FORM` | • `resume_datetime`: *2024‑06‑15 10:25*  <br>• `input_qty`: *112.0* kg | Operator (EMP‑OP‑003) |
| 10:40 | **Finish** drying. | `PROCESS_STEP_FORM` | • `stop_datetime`: *2024‑06‑15 10:40*  <br>• `output_qty`: *110.0* kg | Operator (EMP‑OP‑003) |

---

## 7.  Sieving (Process Step)

| Time | Action (Operator) | **Form** | Form Field(s) Updated | PIC |
|------|-------------------|----------|-----------------------|-----|
| 10:45 | **Start** sieving. | `PROCESS_STEP_FORM` | • `process_id`: *S001*  <br>• `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `input_qty`: *110.0* kg  <br>• `start_datetime`: *2024‑06‑15 10:45*  <br>• **`pic_id`**: *EMP‑OP‑003* | Operator (EMP‑OP‑003) |
| 11:00 | **Finish** sieving. | `PROCESS_STEP_FORM` | • `stop_datetime`: *2024‑06‑15 11:00*  <br>• `output_qty`: *109.0* kg | Operator (EMP‑OP‑003) |

---

## 8.  Packaging & Inventory Move

| Time | Action (Warehouse) | **Form** | Form Field(s) Updated | PIC |
|------|--------------------|----------|-----------------------|-----|
| 11:05 | **Select** SKU *TGLM-PCH-500G*. | `PACKAGING_FORM` | • `sku_id`: **SKU‑TGLM‑PCH‑500G**  <br>• `sku_code`: *TGLM-PCH-500G* | Warehouse Staff (EMP‑WHS‑001) |
| 11:10 | **Start** packaging. | `PACKAGING_FORM` | • `lot_number`: **LOT‑TGLM‑20240615-0001**  <br>• `input_qty`: *109.0* kg  <br>• `start_datetime`: *2024‑06‑15 11:10*  <br>• **`pic_id`**: *EMP‑WHS‑001* | Warehouse Staff (EMP‑WHS‑001) |
| 11:20 | **Stop** for ingredient shortage. | `STOP_RESUME_LOG_FORM` | • `stop_datetime`: *2024‑06‑15 11:20*  <br>• `stop_reason_id`: *Ingredient Shortage*  <br>• **`pic_id`**: *EMP‑WHS‑001* | Warehouse Staff (EMP‑WHS‑001) |
| 11:25 | **Resume** packaging. | `STOP_RESUME_LOG_FORM` | • `resume_datetime`: *2024‑06‑15 11:25*  <br>• `input_qty`: *109.0* kg | Warehouse Staff (EMP‑WHS‑001) |
| 11:45 | **Finish** packaging. | `PACKAGING_FORM` | • `stop_datetime`: *2024‑06‑15 11:45*  <br>• `output_qty`: *108.5* kg (217 units × 500 g) | Warehouse Staff (EMP‑WHS‑001) |
| 11:50 | **Move** to warehouse inventory. | `PACKAGING_FORM` | • `inventory_move`: **To Warehouse**  <br>• **`pic_id`**: *EMP‑WHS‑001* | Warehouse Staff (EMP‑WHS‑001) |

---

## 9.  Lot Sign‑off & Final Reporting

| Time | Action (Supervisor) | **Form** | Form Field(s) Updated | PIC |
|------|---------------------|----------|-----------------------|-----|
| 12:00 | **Verify** all entries against SOP. | `LOT_SIGNOFF_FORM` | • Cross‑check quantities, stoppage logs, and status history. | Supervisor (EMP‑SUP‑002) |
| 12:05 | **Generate** final lot report. | `LOT_SIGNOFF_FORM` | • Includes all process logs, KPI calculations (yield %, loss %). | Supervisor (EMP‑SUP‑002) |
| 12:10 | **Upload** report to the data platform. | `LOT_SIGNOFF_FORM` | • Attach PDF / CSV to the system (web‑form). | Supervisor (EMP‑SUP‑002) |
| 12:15 | **Close** the lot. | `LOT_SIGNOFF_FORM` | • Set `lot_status`: **Closed**  <br>• **`pic_id`**: *EMP‑SUP‑002* | Supervisor (EMP‑SUP‑002) |

---

### Key Points

| SOP Section | Form Used | Purpose |
|-------------|-----------|---------|
| **Raw Material Receiving** | `RAW_RECEIPT_FORM` | Capture lot number, source, quantity, condition. |
| **Issuing to Ingredient Prep** | `ISSUE_RECORD_FORM` | Track status change from *Received* → *Issued*. |
| **Ingredient Preparation** | `INGREDIENT_PREP_FORM` | Generate ING‑Lot, endorse to cooking. |
| **Process Steps (Cooking, Drying, Sieving)** | `PROCESS_STEP_FORM` + `STOP_RESUME_LOG_FORM` | Record start/stop, inputs/outputs, stoppage reasons. |
| **Packaging & Inventory** | `PACKAGING_FORM` + `STOP_RESUME_LOG_FORM` | Capture SKU, quantity packaged, inventory movement. |
| **Lot Creation / Sign‑off** | `LOT_CREATION_FORM` + `LOT_SIGNOFF_FORM` | Generate LOT‑Lot, close lot, generate report. |

All data points are entered into the **physical form** specified above; no new or invented forms were added.  
The production initiation step is now explicitly shown, ensuring the run‑through aligns 100 % with the SOP.