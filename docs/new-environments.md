# Setting Up An Environment For RMI

## Getting Started & Prerequisites

The Report Management Information System is hosted on Cloud Foundry, using the gov GPaaS service layer. Inside of this infrastrucure, we have [Organisations](https://docs.cloud.service.gov.uk/orgs_spaces_users.html#organisations) which will hold the Cloud Foundry [Spaces](https://docs.cloud.service.gov.uk/orgs_spaces_users.html#spaces), or environments, that can be named and created as seen fit. For the RMI project, we have a Organisation called ['ccs-report-management-info'](https://admin.london.cloud.service.gov.uk/organisations/b2daa20a-d281-4874-bd10-0bbb494480bc).

Optionally, you may also need a [Domain](https://docs.cloud.service.gov.uk/deploying_services/configure_cdn/#configure-your-custom-domain-in-cloud-foundry) that you wish to use for routing your apps, in the new environment, is required. This may be an exisiting one, already setup in Cloud Foundry, or a brand new one. New ones must be setup and confirmed with CCS Tech Ops.

As result you will first need both a GOV UK PaaS Account and the Cloud Foundry cli setup. See [Infrastructure](https://crown-commercial-service.github.io/ReportMI-service-manual/#/infrastructure?id=prerequisites) for more information.

Furthermore, a GPaaS user with admin priviliges is required when creating a new Space or Domain in Cloud Foundry.

</br>
</br>

## Create a Space & Domain

A 'Space' in cloud foundry serves as our environments. As such to create a new development or deployment environment, you first need to login to the Gov PaaS Cloud Foundry cli, and then create a new space in your targeted Organisation, (with the correct priviliges level). For this project, we use the 'ccs-report-management-info' org:
```bash
cf login -a api.london.cloud.service.gov.uk -u <USER>
```
```bash
cf create-space <SPACE> -o <ORGANISATION>
```
<sup>^ The optional quota flag is ignored and omitted here.</sup></br>

Once a Space has been created, this will be the starting point for our new environment. You can target into this new environment using the command:
```bash
cf target -s <SPACE>
```
</br>

To list already available Domains, use:
```bash
cf domains
```
Then, if you need to add a new Domain, you can use:
```bash
cf create-domain <DOMAIN>
```

</br>

<sup>* This whole step/section requires a GPaaS Admin, as creating a new Space is a restricted action.</sup></br>
<sup>** New Domains will first need to be setup and confirmed with the CCS Tech Ops team.</sup></br>

</br>
</br>

## Create & Setup Backing Services

The initial steps you must carry out first are automatically done by a set of '.sh' [shell scripts](https://github.com/Crown-Commercial-Service/DataSubmissionService/tree/develop/CF), which are included in the [Front-End](https://github.com/Crown-Commercial-Service/DataSubmissionService) of RMI. 

These shell script are ran from inside terminal/cmd, within the CF/ folder.

</br>

1. Create & Initialise the Space, then configure Domain/CDN:
```bash
./create-cf-space.sh -u <USER> -p <PASSWORD> -o <ORGANISATION> -s <SPACE>
```
2. Once the previous shell script has fully completed, and all operations done, run the next script, which adds and configures all the remaining services:
```bash
./create-user-space.sh -u <USER> -p <PASSWORD> -o <ORGANISATION> -s <SPACE>
```

</br>
</br>

## Pushing The Apps

Next is to push each of the required apps to Cloud Foundry, using the final '.sh' shell script provided in both the [Front-End](https://github.com/Crown-Commercial-Service/DataSubmissionService), and the [Back-End](https://github.com/Crown-Commercial-Service/DataSubmissionServiceAPI), and also the [Router](https://github.com/Crown-Commercial-Service/DataSubmissionServiceRouter) of RMI.

Again, you must be running these shell scripts inside your terminal/cmd, from within the CF/ folder.

</br>

1. Starting in the the [Front-End](https://github.com/Crown-Commercial-Service/DataSubmissionService/blob/develop/CF/deploy-app.sh) CF folder, run:
```bash
./deploy-app.sh -u <USER> -p <PASSWORD> -o <ORGANISATION> -s <SPACE>
```
2. And now again, but from the [API Back-End](https://github.com/Crown-Commercial-Service/DataSubmissionServiceAPI/blob/develop/CF/deploy-app.sh) CF folder, run:
```bash
./deploy-app.sh -u <USER> -p <PASSWORD> -o <ORGANISATION> -s <SPACE>
```
3. Finally the same once more, but for the [Router](https://github.com/Crown-Commercial-Service/DataSubmissionServiceRouter/blob/master/CF/deploy-app.sh), from the CF folder, run:
```bash
./deploy-app.sh -u <USER> -p <PASSWORD> -o <ORGANISATION> -s <SPACE>
```

</br>

<i><b>You will now have 5 applications successfully pushed and running, (seen by running 'cf apps' inside the new space), with a full set of backing services bound & supporting these apps, (seen by running 'cf services' inside the new space).</b></i>

</br>
</br>

## Setup CDN Service & Routing Mapping
Now to setup any routing with a domain and associated CDN service, Use cf create-domain if you need to add a domain, which should be done first, (see above). With any domains you wish to use available, from your desired space, run:

```bash
cf create-service cdn-route cdn-route ccs-rmi-cdn-<SPACE> -c '{"domain": "<URI_1>,<URI_2>,...<URI_n>", "headers": ["*"]}'
```

<sup>^ If you get an error, along the lines of a service already exists, then you can use the "cf delete-service \<SERVICE_NAME>" command, to delete the service causing the problem, (it is an incomplete service).</sup></br>

After this you must bind any apps required to use this new CDN service, and also map these new routes:

```bash
cf map-route <APP_NAME> <DOMAIN> --hostname <WWW_or_API>
```
```bash
cf bind-route-service <DOMAIN> --hostname <WWW_or_API> <SERVICE_NAME>
```

</br>

<sup>* Typically, the service name used for SERVICE_NAME is something along the lines of "ccs-rmi-api-admin-route-\<SPACE>".</sup></br>
<sup>** Note that for the hostname, it is either "API" or "WWW" depending on route and application involved. This does not include a full stop, (this is intentional).</sup>

</br>
</br>

## Add Network Policy

Penultimately, we must create a network policy for our apps cliet and server to communicate with each other. Failure to do this step will result in a 500 Error, accompanied with a Connection Refused. By standard; CCS opens the default 8080 port, so this is the port we will use to setup the tcp network policy, by running:
```bash
cf add-network-policy <PUBLIC_APP_NAME> --destination-app <PRIVATE_APP_NAME> --protocol tcp --port 8080
```

<sup>^ The public app is your client or front-end app, while the private app is your server or back-end app.</sup>

</br>
</br>

## Setup Databases

Lastly, we need to connect to both the Front-End App and the Back-End Api applications running in your new space/environment, and setup the databases, through their schemas. To do that we first enable a couple of plugins/extensions that are required, (and not enabled by default). Run both these commands from your terminal/cmd, inside the space/environment:
```bash
cf update-service ccs-rmi-app-<SPACE> -c '{"enable_extensions":["pgcrypto","plpgsql"],"reboot":true}'
```
```bash
cf update-service ccs-rmi-api-<SPACE> -c '{"enable_extensions":["pgcrypto","plpgsql"],"reboot":true}'
```

</br>

Next, connect to both the App and Api applications. Connect by ssh, match the app's environment to yours, and finally run/load the database schema:
```bash
cf v3-ssh <APPLICATION>
```
```bash
/tmp/lifecycle/shell
```
```bash
bin/rails db:schema:load
```

</br>
<hr>
</br>

<i><b><u>And with that, you should now have a fully functioning environment, all good to go. The next steps would be visit both front-end and back-end urls, to ensure they are working, and then to onboard yourself as a user, via logging into the back-end api admin app.</u></b></i>