# Infrastructure

This document describes the infrastructure of the LibrOpen project.

## Overview

The infrastructure of the LibrOpen project is based on the following components:

- **GitHub**: The main repository of the project is hosted on GitHub. The project uses GitHub Actions for continuous integration and deployment.
- **Jenkins**: The project uses Jenkins for continuous integration and deployment, the jenkins server is hosted on a virtual machine accessible with the following URL: [http://jenkins.libropen.com](http://jenkins.libropen.com).
- **Docker**: The project uses Docker for containerization. The Docker images are hosted on our own registry accessible with the following URL: [https://registry.libropen.com](https://registry.libropen.com).
- **MiniIO**: The project uses MiniIO for object storage. The MiniIO server is hosted on a virtual machine accessible with the following URL: [http://minio.libropen.com](http://minio.libropen.com).
- **OpenVPN**: To access the ressources of the infrastructure, you need to connect to the OpenVPN server hosted on a virtual machine accessible with a profile file provided by the team.
- **Hoppscotch**: The project uses Hoppscotch for API testing. The Hoppscotch server is hosted on a virtual machine accessible with the following URL: [http://hoppscotch.libropen.com](http://hoppscotch.libropen.com).
- **Mail Server**: The project uses a mail server to send emails to the users. The mail server is hosted on a virtual machine accessible with the following URL: [http://mail.libropen.com/mail](http://mail.libropen.com/mail).

## GitHub

The main repository of the project is hosted on GitHub. The project uses GitHub Actions for continuous integration and deployment. The GitHub Actions are defined in the `.github/workflows` directory. The GitHub Actions are triggered on every push to the repository. There are two main workflows:

- **backend.yml**: This workflow is triggered on every push to the `backend` directory. It tries to build the backend application and run the tests and linter. It also pushes changes made by the linter to the git repository.
- **frontend.yml**: This workflow is triggered on every push to the `frontend` directory. It tries to build the frontend application and run the tests and linter. It also pushes changes made by the linter to the git repository

## Jenkins

The project uses Jenkins for continuous integration and deployment. The jenkins server is hosted on a virtual machine accessible with the following URL: [http://jenkins.libropen.com](http://jenkins.libropen.com). The Jenkins server is configured to run the following jobs:

- **Deployment**: This job is triggered on every push to the `main` branch. It tries to build the backend and frontend applications and deploy them to the staging server. It also pushes the built docker images as artifact and to the self hosted docker registry.

## Docker

The project uses Docker for containerization. The Docker images are hosted on our own registry accessible with the following URL: [https://registry.libropen.com](https://registry.libropen.com). The Docker images are built using the GitHub Actions and pushed to the registry.

There is also a docker-compose file at the root of the repository that can be used to run the project locally. The docker-compose file defines the following services:

- **postgres**: The postgres database used by the backend application.
- **frontend**: The frontend application.
- **backend**: The backend application.
- **backend_migration**: The backend migration service, used to apply the database migrations.
- **pgadmin**: The pgadmin service, used to manage the postgres database using a web interface.

There are also a network and a volume defined in the docker-compose file, used for file sharing and communication between the services.

## MiniIO

The project uses MiniIO for object storage. The MiniIO server is hosted on a virtual machine accessible with the following URL: [http://minio.libropen.com](http://minio.libropen.com). The MiniIO server is configured to use the following buckets:

- **libropen**: The main bucket used by the project to store files.

## OpenVPN

To access the ressources of the infrastructure, you need to connect to the OpenVPN server hosted on a virtual machine accessible with a profile file provided by the team. If you don't use the profile file, the firewall will block all the incoming and outgoing traffic.

## Hoppscotch

The project uses Hoppscotch for API testing. The Hoppscotch server is hosted on a virtual machine accessible with the following URL: [http://hoppscotch.libropen.com](http://hoppscotch.libropen.com). We use a shared workspace called 'LibrOpen' to share the API requests between the team members. If you want to join the workspace, please contact the team (admin@libropen.com).

## Mail Server

The project uses a mail server to send emails to the users. The mail server is hosted on a virtual machine accessible with the following URL: [http://mail.libropen.com/mail](http://mail.libropen.com/mail). The mail server is configured to use the following email addresses: teammembername@libropen.com
For example if your name is John Doe, your email address will be
john@libropen.com

