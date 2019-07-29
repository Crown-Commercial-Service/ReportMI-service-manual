# 27. stop-using-docker

Date: 2019-07-29

## Status

Accepted

## Context

- previously both Rails applications advised against using Docker locally, this likely lead to the docker option no longer working out of the box for new joiners:
  - https://github.com/dxw/DataSubmissionService/pull/263/files#diff-04c6e90faac2675aa89e2176d2eec7d8L3
   - https://github.com/dxw/DataSubmissionServiceAPI/pull/477/files#diff-04c6e90faac2675aa89e2176d2eec7d8L3
- containers are now no longer used in staging or production either [after the recent move from AWS ECS](TODO)

## Decision

Remove Docker from the applications.

## Consequences

- moving from GOV.UK PaaS back to AWS ECS or another container hosting platform becomes harder
- without maintaining two versions of set up and run commands, the team will be able to focus on only ensuring the non-docker set up documentation is maintained
- new joiners are clear on what they should use locally and can get started quickly, without spending time trying to make the docker version work again
- should containerisation be needed again, the removed code can be referenced https://github.com/dxw/DataSubmissionServiceAPI/pull/477 and https://github.com/dxw/DataSubmissionService/pull/263, or their merge commits even reverted to restore the code
