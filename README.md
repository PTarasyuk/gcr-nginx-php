# gcr-php-nginx

This repository contains all the necessary components for deploying a simple PHP application on Google Cloud Run using a multi-container approach with Nginx and PHP-FPM.
The project is designed to demonstrate how to efficiently use Google Cloud Run to deploy lightweight web applications with high availability and scalability.

## Key Features

- **Ease of Deployment**: Detailed deployment instructions ensure a seamless process from development to production.
- **High Customizability**: Using Docker containers for Nginx and PHP-FPM allows for easy configuration adjustments of the web server and PHP.
- **Automatic Scaling**: Leverages Google Cloud Run's capabilities for automatic scaling based on traffic.
- **High Availability**: Ensures high availability of the application by deploying on Google Cloud Run.

## Technologies

- **Google Cloud Run**: A platform for deploying containerized applications.
- **Nginx**: A high-performance web server and reverse proxy.
- **PHP-FPM**: An advanced process manager for PHP, optimized for heavy traffic.
- **Docker**: A platform for developing, shipping, and running applications in containers.
- **GitHub Actions**: An automation tool for CI/CD processes, integrated directly into GitHub.

## Getting Started

1. Update Google Cloud CLI: `gcloud components update`

2. Configure Google Cloud CLI: `gcloud init`

3. Authenticate with Google Cloud CLI: `gcloud auth login`

4. Get the ID of the Google Cloud project

   ```sell
   gcloud projects list
   ```

   or create a new project

   ```shell
   gcloud projects create <Yor_Project_ID> --name=<Your_Project_Name>
   ```

5. Create `.env` file in the root directory of your project. Add environment-specific variables on new line in the form of `NAME=VALUE`:

   ```env
   PROJECT_ID="Your_Project_ID"
   REGION="Your_GCP_Region"
   REPO_NAME="default"
   SERVICE_NAME="Your_Service_Name"
   ```

   Once you have created the `.env` file, you can use the `export` command to temporarily use these environment    variables in your session:

   ```shell
   export $(xargs < .env)
   ```

6. Make sure that billing is enabled for Google Cloud project: `gcloud beta billing projects describe ${PROJECT_ID}`

7. Set Google Cloud CLI properties: `gcloud config set project ${PROJECT_ID}`

8. Enable the Cloud Run and Secret Manager APIs: `gcloud services enable run.googleapis.com artifactregistry.googleapis.com`

9. Create a repository within Artifact Registry: `gcloud artifacts repositories create ${REPO_NAME} --repository-format=docker`

10. Build the `nginx` and `php-app` container images for our Cloud Run service:

   ```shell
   gcloud builds submit --tag=${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO_NAME}/nginx ./nginx
   gcloud builds submit --tag=${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO_NAME}/php-app ./php-app
   ```

## Configure and deploy the service

You will see as you read through `service.yml`, that the memory limit has been explicitly set to `335M`.
This levels ~143M for PHP processes after allocating 192M for opcache.
See how we got [here](https://cloud.google.com/run/docs/configuring/services/memory-limits#optimizing) and read more about how to [optimize for concurrency](https://cloud.google.com/run/docs/tips/general#optimize_concurrency).

> [!NOTE]
> This project does not contain extra configuration to tune php-fpm settings to specify the number of workers to correlate with the container concurrency.

Deploy the Cloud Run service from root directory:

```shell
envsubst < service.yml | gcloud run services replace - --region=${REGION}
```

By default, the above command will deploy the following containers into a single service:

- `nginx`: serving ingress container (entrypoint)
- `php-app`: application container

The Cloud Run Multi-container service will default access to port `8080`, where `nginx` container will be listening and proxy request over to `php-app` container at port `9000`.

Verify by using curl to send an authenticated request:

```shell
curl --header "Authorization: Bearer $(gcloud auth print-identity-token)" $(gcloud run services describe ${MC_SERVICE_NAME} --region=${REGION} --format='value(status.url)')
```

To grant all Internet users permission to invoke the Cloud Run service with the name `${SERVICE_NAME}` in the region `${REGION}`, execute the following command:

```shell
gcloud run services add-iam-policy-binding ${SERVICE_NAME}  --member=allUsers --role=roles/run.invoker --region=${REGION}
```

### Clear

```shell
gcloud run services delete ${SERVICE_NAME} --region=${REGION}
gcloud artifacts repositories delete ${REPO_NAME} --location=${REGION}
gcloud services disable run.googleapis.com artifactregistry.googleapis.com --force
```

or

```shell
gcloud projects delete ${PROJECT_ID}
```

## Contributing

We welcome any contributions to the project, whether it be bug fixes, feature additions, or documentation improvements. Please submit pull requests or create issues with your suggestions.
