# 26. use-govuk-paas-for-hosting

Date: 2019-07-29

## Status

Accepted

## Context

- previously in July 2018 [a decision was made to use AWS ECS](https://github.com/Crown-Commercial-Service/ReportMI-service-manual/blob/master/docs/adr/0012-use-ecs-for-hosting-applications.md) for hosting this service
- [GOV.UK PaaS is an existing platform as a service](https://www.cloud.service.gov.uk/) that CCS are able to take advantage of
- GOV.UK PaaS does not currently support containers, whereas ECS is container based
- GOV.UK PaaS costs more or less? TODO
- TODO …

## Decision

Switch the hosting of this service from AWS ECS to GOV.UK PaaS, this includes all applications and environments.

## Consequences

- the existing docker configuration is now obsolete
- without containers we will lose the advantages such as ease of set up, environment parity and infrastructure portability previously outlined in [the ADR that decided to use it originally](https://github.com/Crown-Commercial-Service/ReportMI-service-manual/blob/master/docs/adr/0005-use-docker-for-applications.md)
- TODO …
