## 2. Maintain corrections process consistent with MISO

Date: 2018-09-12

## Status

Working assumption

## Context
Until April 2019, the existing MISO system and the new Report MI service will be running in parallel. Both will be shipping data downstream, to Coda (later Workday) and Data Warehouse.

During this transition period, suppliers may make incorrect returns which need correction, either inside the submission window (before downstream export) or outside it (after data has been shared downstream). It is also possible that during the continued iterative development of the new Report MI service, required data validations may be incorrectly or inconsistently applied, requiring correction in the same way as supplier errors.

The new service will capture and retain all submissions, including ones later corrected, to support auditing and reconciliation activities. The MISO system does not do this.

## Decision

We will follow the same process for correction as the MISO system. Namely:
- supplier reports error or internal validation error is detected
- incorrect return is deleted and supplier is asked to upload a new, corrected return
- finance and data intelligence teams are notified to enable appropriate downstream steps

## Consequences

We will need to continue to evaluate the quality and application of downstream data exports to ensure this process is fit for purpose.

We may need to review this decision during the course of ongoing iterative development to refine the way templates are built, and submissions are validated and stored by the new service, which may render some or all of the correction process redundant.

We expect this approach to change when the Workday integration is complete [link to Workday ADR] and credit notes and invoice creation will be automated from RMI to Workday.
