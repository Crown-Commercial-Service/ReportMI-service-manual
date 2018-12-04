# 5. Returns must always be filed by suppliers 

## Status

Accepted

## Context

Having collected returns for 3 months from over 100 suppliers, we have found that a small subset of suppliers do not reliably submit returns. When prompted by Category Managers, these suppliers often respond via email to state that they have not done any business, but do not submit this information in the service. 

## Decision
The Report MI service team will not perform no business returns on suppliers' behalf, or upload files on suppliers' behalf, and will not develop any admin tooling to enable this. The accountability for submitting correct returns should lie with Suppliers, and the confirmation step is crucial to ensuring that returns can be effectively audited and legally binding. 

Suppliers must be encouraged to log in to the service and submit their own returns. 

This will also apply to agreements where suppliers are not obliged to submit 'no business' returns. We won't capture these as 'no business' as the current system does, but retain them as a 'not submitted' state. 

## Consequences

CCS staff who interact with suppliers may need to modify their messaging to suppliers when this occurs, to direct them to use the service or risk removal from the framework for repeated failure to submit a return. 

Frequently missed returns by a single suppliers will generate a very long task list in Report MI, and we'll need to think about how we handle this in the service. 

As part of this consideration, we will also need to ensure there is a way for support or category teams within CCS to dismiss the tasks for frameworks where 'no business' returns are optional. 

We will also need to decide how to handle this when frameworks expire, or when suppliers leave or are removed from a framework. 

We will need to create a new state in the service to distinguish a non return from a confirmed no business return
