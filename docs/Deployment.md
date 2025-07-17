# <a name="Deployment"></a>Deployment

The plugin is distributed as a container image.

## Example deployment steps

There are multiple ways to deploy an application in a container, and multiple cloud providers, so we're unable to provide comprehensive instructions for all combinations. However, here is how you might go about deploying the image to Google Cloud Run

These steps assume you a Google Cloud project with access to the appropriate APIs and have gcloud installed and configured to point at it

### Get the plugin container image

Download the plugin container image at [https://storage.googleapis.com/klarnact-container-repository/klarna-ct-plugin_1.0.6.tar](https://storage.googleapis.com/klarnact-container-repository/klarna-ct-plugin_1.0.6.tar)

Push the image into Google Container Registry [Pushing and pulling images](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

### Create secrets file

This is only required once unless the values change. Create an application.yml file with the following

```
commercetools:
  client-id: <your commercetools API key client id>
  client-secret: <your commercetools API key client secret>
klarna:
  zone:
    EU:
      auth:
        username: <your Klarna EU MID username>
        password: <your Klarna EU MID password>
    NA:
      auth:
        username: <your Klarna NA MID username>
        password: <your Klarna NA MID password>
    OC:
      auth:
        username: <your Klarna OC MID username>
        password: <your Klarna OC MID password>
```

Create a Google Cloud secret to store it (only needs to be done once, even when updating)

Replace &lt;secret name&gt; with a name you will use for the secret

`gcloud secrets create <secret name> --replication-policy="automatic"`

Set the secret content

`gcloud secrets versions add <secret name> --data-file="path/to/application.yml"`

### Deploy to CloudRun

Replace &lt;instance name&gt; with a name you want for the Cloud Run instance. Other values should be substituted as above

```
gcloud beta run deploy <instance name> \
  --region europe-west1 \
  --image "eu.gcr.io/<project name>/klarna-ct-plugin:<version>" \
  --platform managed \
  --allow-unauthenticated \
  --project <project name> \
  --set-env-vars 'COMMERCETOOLS_PROJECT_KEY=<your commercetools project key>' \
  --set-env-vars "COMMERCETOOLS_AUTH_URL=<commercetools auth URL>" \
  --set-env-vars "COMMERCETOOLS_API_URL=<commercetools API URL>" \
  --set-env-vars "KLARNA_ZONE_EU_BASE_URL=<Klarna API EU base URL>" \
  --set-env-vars "KLARNA_ZONE_NA_BASE_URL=<Klarna API NA base URL>" \
  --set-env-vars "KLARNA_ZONE_OC_BASE_URL=<Klarna API OC base URL>" \
  --set-secrets=/secrets/application.yaml=<secret name>:latest
```
