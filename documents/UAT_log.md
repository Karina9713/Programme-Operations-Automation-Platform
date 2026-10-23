# User Acceptance Testing (UAT) Log

**Project:** Programme Operations & Workflow Automation Suite

**Test Environment:** Microsoft Lists, Power Automate, Power BI Desktop

**Test Data:** Fictional demonstration records

## 1. Testing Objective

To validate end-to-end data integrity, application reporting accuracy, session status tracking, rule-based facilitator fee calculations, and automated cross-list record synchronisation across Microsoft Lists, Power Automate, and Power BI.

## 2. Test Cases and Execution Results

| Test ID | System Component | Test Scenario & Trigger | Expected Result | Actual Result | Status | 
| ----- | ----- | ----- | ----- | ----- | ----- | 
| **UAT-01** | Microsoft Form / Microsoft Lists / Power BI | Ingest test Programme A (Group Programme) requests across varying statuses (`Pending`, `Confirmed`, `Rejected`, `Waitlisted`). | Microsoft List and Power BI Programme A view displays exact request counts and status distributions. | Programme A List and Power BI report views filtered and calculated all status categories accurately. | **Pass** | 
| **UAT-02** | Microsoft Form / Microsoft Lists / Power BI | Ingest test Programme B (Guided Visit) requests across varying statuses. | Microsoft List and Power BI Programme B view displays exact request counts and status distributions without exposing Programme A data. | Programme B List and Power BI report views rendered correct isolated records and status distributions. | **Pass** | 
| **UAT-03** | Power BI | Apply interactive slicers and filters on the Programme B team view. | Visuals dynamically filter and restrict display to matching Programme B records only. | Slicers correctly updated all page visuals without cross-filtering bleed. | **Pass** | 
| **UAT-04** | Power BI / DAX | Add a confirmed booking with `Allocation Status` set to `Pending`. | The session is flagged in the DAX measure `Confirmed Sessions Awaiting Staff`. | `Confirmed Sessions Awaiting Staff` measure incremented by 1 immediately upon refresh. | **Pass** | 
| **UAT-05** | Power BI / DAX | Transition a confirmed session to `Cancelled` status. | Session is excluded from `Upcoming Sessions` count, and `Late Cancellation Fees` updates if eligible. | Session was filtered out of active counts, and cancellation fee DAX measure recalculated correctly. | **Pass** | 
| **UAT-06** | Power BI / DAX | Evaluate guide-fee eligibility rules between Programme A and Programme B. | Fee DAX measures sum only records where `[Cancellation Fee Eligibility] = TRUE()`. | Fee calculation measures correctly isolated eligible records across both programme types. | **Pass** | 
| **UAT-07** | Power Automate | Update a Programme request to `Confirmed` status in List 1 or List 2. | Power Automate triggers, verifies duplicate rules, and creates a linked record in List 3 (`Facilitator Assignment`). | Record successfully created in List 3 with mapped `Request ID` and initialised statuses. | **Pass** | 
| **UAT-08** | Power Automate / Lists / Power BI | Modify event date/time or status in List 1/2 for a session that already has a List 3 record. | Power Automate updates the corresponding record in List 3, maintaining bi-directional consistency in Power BI. | All modified fields synchronised to List 3 and accurately reflected in Power BI upon refresh. | **Pass** | 

## 3. User Acceptance Summary

* **Total Test Cases Executed:** 8

* **Passed:** 8

* **Failed:** 0

* **Pass Rate:** 100%

### Verification Conclusion

All eight UAT test scenarios met their defined acceptance criteria. The execution records confirm that system boundary separation, automated record creation, bi-directional field synchronisation, and Power BI DAX calculations function reliably under tested operational scenarios.