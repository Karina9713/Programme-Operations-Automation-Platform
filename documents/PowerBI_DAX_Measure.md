# Power BI DAX Measures & Analytics Logic

This document provides the complete technical definitions, formulas, and operational context for the custom DAX measures developed for the **Programme Operations Automation Suite**. These measures power the Power BI reporting layer by handling session monitoring, staffing coordination, and automated rule-based fee calculations.

---

## 1. Total Facilitator Fee Payable
* **Purpose:** Calculates the total payable Facilitator fees for eligible assignment requests across operations.
* **DAX Formula:**
  ```dax
  Total Facilitator Fee Payable = 
  SUMX(
      FILTER(
          'Facilitator Assignment',
          'Facilitator Assignment'[Cancellation Fee Eligibility] = TRUE()
      ),
      'Facilitator Assignment'[Required Resources] * AMOUNT
  )
  ```
* **Logic & Business Rules:**
  * Filters the `'Facilitator Assignment'` table to include only records marked as eligible for fee payment.
  * Multiplies the number of required resources (`[Required Resources]`) by the standard rate per Facilitator.
  * Sums the calculated fees across all eligible records.

---

## 2. Upcoming Sessions
* **Purpose:** Counts distinct allocation references for active sessions scheduled today or in the future, excluding cancelled bookings.
* **DAX Formula:**
  ```dax
  Upcoming Sessions = 
  CALCULATE(
      DISTINCTCOUNT('Facilitator Assignment'[Allocation Reference]),
      'Facilitator Assignment'[Session Date] >= TODAY(),
      'Facilitator Assignment'[Session Status] <> "Cancelled"
  )
  ```
* **Logic & Business Rules:**
  * Uses `DISTINCTCOUNT` on `[Allocation Reference]` to ensure unique assignment records are not double-counted.
  * Filters for event dates occurring on or after the current date (`TODAY()`).
  * Excludes any sessions flagged as `"Cancelled"`.

---

## 3. Cancelled Sessions
* **Purpose:** Counts the total number of assignment request records that have been cancelled.
* **DAX Formula:**
  ```dax
  Cancelled Sessions = 
  CALCULATE(
      COUNTROWS('Facilitator Assignment'),
      'Facilitator Assignment'[Session Status] = "Cancelled"
  )
  ```
* **Logic & Business Rules:**
  * Filters the `'Facilitator Assignment'` table to isolate cancelled sessions.
  * Evaluates `COUNTROWS` to provide a baseline metric for cancellation volume and exception tracking.

---

## 4. Late Cancellation Fees
* **Purpose:** Calculates payable Facilitator fees for cancelled sessions that satisfy the late-cancellation eligibility policy.
* **DAX Formula:**
  ```dax
  Late Cancellation Fees = 
  CALCULATE(
      SUMX(
          'Facilitator Assignment',
          'Facilitator Assignment'[Required Resources] * AMOUNT
      ),
      'Facilitator Assignment'[Session Status] = "Cancelled",
      'Facilitator Assignment'[Cancellation Fee Eligibility] = TRUE()
  )
  ```
* **Logic & Business Rules:**
  * Restricts evaluation strictly to records where `[Session Status] = "Cancelled"` and `[Cancellation Fee Eligibility] = TRUE()`.
  * Computes financial exposure by multiplying required resources by Facilitator fee.
  * *Note on Automation:* The cancellation rule logic is evaluated and stamped upstream by the Power Automate workflow, which dynamically sets the `[Cancellation Fee Eligibility]` boolean field.

---

## 5. Confirmed Sessions Awaiting Staff
* **Purpose:** Identifies confirmed Programme B applications that have not yet been assigned a facilitator (i.e., allocation request is still pending).
* **DAX Formula:**
  ```dax
  Confirmed Sessions Awaiting Staff = 
  CALCULATE(
      DISTINCTCOUNT('Programme B Requests'[Request Reference]),
      'Programme B Requests'[Request Status] = "Confirmed",
      'Programme B Requests'[Allocation Status] = "Pending"
  )
  ```
* **Logic & Business Rules:**
  * Scans the `'Programme B Requests'` list table.
  * Filters for records where application status is confirmed (`"Confirmed"`) and resource allocation remains pending (`"Pending"`).
  * Counts distinct request references to highlight active staffing bottlenecks for operations teams.

---

## Summary Matrix

| Measure Name |  Reporting Purpose |
| :--- | :--- |
| **Total Facilitator Fee Payable** |  Estimates financial commitments for all eligible active and completed requests. |
| **Upcoming Sessions** | Monitors forward-looking operational volume and active event schedules. |
| **Cancelled Sessions** | Tracks total cancellation volume for exception reporting and trend analysis. |
| **Late Cancellation Fees** | Calculates recoverable or payable late fees based on lead-time eligibility flags. |
| **Confirmed Sessions Awaiting Staff** | Highlights confirmed bookings that require immediate facilitator assignment attention. |
