# Infrastructure access

The Data Submission Service is hosted on [GOV.UK's Platform as a Service](https://www.cloud.service.gov.uk/) which is based on [Cloud Foundry](https://www.cloudfoundry.org/).

[Documentation for interacting with the PaaS can be found on GOV.UK](https://docs.cloud.service.gov.uk/).

## Prerequisites

1. GOV.UK PaaS account - dxw technical operations can create this account for you and you'll recieve an email
2. [install command line tools](https://docs.cloud.service.gov.uk/get_started.html#get-an-account) for shell access

## Get shell access on a box

Login to the right Cloud Foundary account
```bash
$ cf login -a api.london.cloud.service.gov.uk -u <YOUR_EMAIL_ADDRESS>
```

Select the right environment using `cf target`

```bash
$ cf target -s staging
```

SSH onto an application running within that environment
```bash
$ cf v3-ssh ccs-rmi-api-staging
```

Start a Rails console

```bash
$ /tmp/lifecycle/shell
$ bin/rails console
```

## Viewing environment variables

The [GOV.UK PaaS documentation for viewing the current value for variables](https://docs.cloud.service.gov.uk/deploying_apps.html#environment-variables) can be used.

For example you can view staging variables by:

```
cf login -a api.london.cloud.service.gov.uk -u <YOUR_EMAIL_ADDRESS>
cf target -s staging
cf v3-apps
cf env APP_NAME
```
