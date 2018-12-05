# 6. Don't validate customer postcode field

## Status

Accepted

## Context

We currently have a validation rule that checks whether what user submits in the postcode field is actually shaped like a postcode.
However, we now know that the postcodes submitted by suppliers are overwritten in both finance and Data Warehouse systems, with the postcode stored against the Customer URN.
After speaking to the MI Collection Team, we also know that newer templates from the last 18 months don't collect this field at all. The team said that this data field is of minimal importance to them.

## Decision

There are two decisions that we would like to take given the circumstances mentioned above.

1. We will stop validating the postcode field.
2. We will start ignoring the postcode field, as we should not be collecting any data from suppliers that we do not have any need for. This is in line with GOV.UK Service Manual too.

We can start ignoring the postcode field, as long as Finance and Data Warehouse systems can still overwrite this field even if it has no data on it.
If this field needs to have a value with it for the downstream systems (Finance and Data Warehouse) to overwrite it, then we will accept whatever value the user has submitted as their postcode.

## Consequences

Any edge cases of postcode submission that might not fit the MISO pattern will still be accepting, making it possible for the user to submit their MI, which is the ultimate goal.

Workday still needs to perform this overwriting in the same way CODA did.
