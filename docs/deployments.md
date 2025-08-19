# Release process

## Update CHANGELOG

As outlined in the [DSD code management workflow guide], Release/* branches are protected now so we can no longer push directly to them. To avoid having to make another pull request just for CHANGELOG additions at the end, it’s may be more efficient to update it as we go in Feature/* branches.

In CHANGELOG.md:
  - document the change your feature contributes to the release in a bullet point form
  - Make sure there’s a corresponding link to the diff at the bottom of the file

## Prepare release notes

- Create [release versions](https://crowncommercialservice.atlassian.net/projects/NRMI?selectedItem=com.atlassian.jira.jira-projects-plugin%3Arelease-page&status=no-filter) for API and Frontend.
- Add the appropriate 'Fix versions' to all relevant tickets. 

## Deploy to production

Having tested in staging, reviewed and merged the relevant Feature/* branches back into develop: 

### 1. Create a Release/* branch

  - Create a branch from `develop` for the release called `Release/X` where X is the release number

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

### 5. Deploy the master branch in Jenkins

  - Trigger the build and deploy pipeline in jenkins through 'Build with parameters':
    - [API](https://jenkins.ai-cloud.uk/job/rmi/job/build-and-deploy-pipeline/job/build-and-deploy-pipeline-api/)
    - [Frontend](https://jenkins.ai-cloud.uk/job/rmi/job/build-and-deploy-pipeline/job/build-and-deploy-pipeline-frontend/)

### 6. Run any new migrations if required

  - See [guidance](infrastructure.md) on manually running migrations 

### 7. Production smoke test

Once the code has been deployed to production, carry out a quick smoke test to
confirm that the changes have been successfully deployed.

### 8. Create release notes

- Release the new [release versions](https://crowncommercialservice.atlassian.net/projects/NRMI?selectedItem=com.atlassian.jira.jira-projects-plugin%3Arelease-page&status=no-filter), and automatically generate release notes. Make sure they sit in the correct [confluence space](https://crowncommercialservice.atlassian.net/wiki/spaces/CSD/pages/4589879338/RMI+Release+Notes).
- General guidance [here](https://crowncommercialservice.atlassian.net/wiki/spaces/CSD/pages/4591386644/Release+Notes).

[DSD code management workflow guide]:https://crowncommercialservice.atlassian.net/wiki/spaces/DSD/pages/812122139/Code+management+workflow
