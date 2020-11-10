# Release process

The platform is currently hosted on the Government Platform as a Service (GPaaS).

Cloud Foundary scripts exist within each application in the /CF directory. These scripts are responsible for infrastructure changes as a separate task.

## Migrations

Migrations are manual.

1. [get access to a rails console on the box](infrastructure.md)
1. run `bin/rails db:migrate`
1. run `cf v3-zdt-restart ccs-rmi-api-staging` for these changes to take effect

## CHANGELOG

As outlined in the [DSD code management workflow guide], Release/* branches are protected now so we can no longer push directly to them. To avoid having to make another pull request just for CHANGELOG additions at the end, it’s may be more efficient to update it as we go in Feature/* branches.

In CHANGELOG.md:
  - document the change your feature contributes to the release in a bullet point form
  - Make sure there’s a corresponding link to the diff at the bottom of the file

## Staging

Deployments to staging are automatic when new changes are pushed via
pull request to the develop branch.

## Production

Having tested in staging, reviewed and merged the relevant Feature/* branches back into develop: 

### 1. Create a Release/* branch

  - Create a branch from `develop` for the release called `Release/X` where X is the release number
  - Test the release cut in the preprod environment.

### 2. Create a tag for the release:
```bash
git tag -a release-xx -m "release-xx"
git push origin --tags
```

### 3. Confirm the release candidate and perform any prerequisites

  - Confirm the release with any relevant people (product owner, delivery
    manager, etc)
  - Think about any dependencies that also need considering: dependent parts
    of the service that also need updating; environment variables that need
    changing/adding; third-party services that need to be set up/updated

### 4. Make a pull request to merge Release/* branch into master

  - Get the pull request reviewed to confirm that the changes in the release
are safe to ship and that CHANGELOG.md accurately reflects the changes
included in the release.

### 5. Make a pull request to merge Release/* branch back into develop

  - Get the pull request reviewed and approved.

### 6. Production smoke test

Once the code has been deployed to production, carry out a quick smoke test to
confirm that the changes have been successfully deployed.

### 7. Update Jira

Update Jira to reflect the newly deployed tickets.

[DSD code management workflow guide]:https://crowncommercialservice.atlassian.net/wiki/spaces/DSD/pages/812122139/Code+management+workflow
