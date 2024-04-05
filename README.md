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

Before you begin, make sure you have an active Google Cloud account and have configured the `gcloud` CLI. To deploy the project, follow these steps:

- Clone the repository to your machine.
- Configure `gcloud` to work with your account and project.
- Run the deployment script, which will build Docker images for Nginx and PHP-FPM and deploy them to Google Cloud Run.

## Contributing

We welcome any contributions to the project, whether it be bug fixes, feature additions, or documentation improvements. Please submit pull requests or create issues with your suggestions.
