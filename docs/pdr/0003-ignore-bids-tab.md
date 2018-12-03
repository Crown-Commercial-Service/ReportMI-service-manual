# 3. Ignore 'Bids' tabs 

Date: 2018-10-24

## Status

Accepted

## Context

Some templates include a `Bids` tab alongside the more standard `Orders` and `Invoices` tabs. 

These do not have a consequence for spend calculations, but are used to capture which suppliers are being invited to bid on which opportunities. 

There are also individual columns in some templates which are no longer required, and are marked 'do not complete'.

## Decision

Echoing the decision taken by CCS Senior Leadership Team on 16th May that

"We will collect monthly supplier returns for two data types only:
Contracts - to support validation of pipeline analysis
Invoices - to support ongoing levy collection"

Report MI will not ingest data from tabs other than Invoices and Orders. 

We will not build mechanisms for ingesting, validating or otherwise storing Bids data. We will take the same approach to no longer required columns in templates marked 'do not complete'. This is less development effort and has the same result as defining these fields by a ruleset that considers them 'mandatorialy not required'

The majority of frameworks do not need or use this data, making it difficult for us to justify the investment of time and development effort required to continue to process this data in Report MI. 

## Consequences

Comms category currently use this tab to monitor compliance with their frameworks' requirement that all suppliers are invited to bid for all opportunities. If this is a continuing need, we would like to explore alternative ways of capturing and monitoring this. We will explore this need in more detail with these frameworks. 

We believe the Category team for RM3741 also use the data for Commercial Benefits information & calculations. It is our assumption that the improved data quality enabled by Report MI will support better forecasting and projections, removing the need for this data to be collected in this way. 
