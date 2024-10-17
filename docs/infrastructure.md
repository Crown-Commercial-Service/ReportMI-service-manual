# Infrastructure access

RMI is hosted on AWS, general documentation can be found [here](https://docs.aws.amazon.com/)

## Prerequisites

1. AWS account - TechOps can set you up with access to RMI.
2. [install command line tools](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

## Access a running instance

Login via the command line
```bash
$ aws --profile <PROFILE> ecs execute-command --cluster <CLUSTER> --task <TASK> --container admin --interactive --command "/bin/sh"
```
PROFILE is as set in your ~/.aws/config file
CLUSTER can be found in the console at Amazon Elastic Container Service > Clusters, then TASK can be found by copying the arn of the appropriate task.

To check a database migration status

```bash
$ rails db:migrate:status
```

To run a database migration 

```bash
$ rails db:migrate:up VERSION=<VERSION_NUMBER>
```

To start a Rails console

```bash
$ bin/rails console
```
