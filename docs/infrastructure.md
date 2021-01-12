# Infrastructure access

The Data Submission Service is hosted on [GOV.UK's Platform as a Service](https://www.cloud.service.gov.uk/) which is based on [Cloud Foundry](https://www.cloudfoundry.org/).

[Documentation for interacting with the PaaS can be found on GOV.UK](https://docs.cloud.service.gov.uk/).

## Prerequisites

1. GOV.UK PaaS account - the lead or senior developer can create this account for you and you'll recieve an email
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

## Change an environment variable

We use [user provided services](https://docs.cloudfoundry.org/devguide/services/user-provided.html) to manage environment variables on the PaaS, this is different to the [default documentation](https://docs.cloud.service.gov.uk/deploying_apps.html#environment-variables). 

### Prerequisites

#### Secrets

Obtain the environment variable files. Ask a team mate for their location.

#### Scripts

The scripts that help us make these changes are found in the [frontend repository within the CF directory](https://github.com/Crown-Commercial-Service/DataSubmissionService/tree/develop/CF). You should have this repository and directory checked out to continue.

#### JSON Parser

```
brew install jq
```

### Making a change

1. Make a change to the environment variable file
2. Copy the contents into a new local file `DataSubmissionService/CF/.env.cf.staging`
3. Run the script to apply the changes `./create-user-services.sh -u <YOUR_PAAS_EMAIL_ADDRESS> -p <YOUR_PAAS_PASSWORD> -o ccs-report-management-info -s staging`

### Verify the change

```
cf v3-apps
cf v3-env <APP_NAME>
```

### Deploy the change

Using V3 instead of the previous blue/green plugin allows us to execute deployments without any downtime to the user natively.

ZDT = Zero downtime deploy

```
cf v3-apps
cf v3-zdt-restart <APP_NAME>
```

### Watch the deployment progress

You should see the process count for the intended application change as new processes are spun up and handed over to. This can take a few minutes.

```
watch cf v3-apps
```

Once this is complete the containers should be running the new environment variables.

## Restart an application

Redeploy an application from v3-apps with downtime

```
cf v3-apps
cf restage <APP_NAME>
```
