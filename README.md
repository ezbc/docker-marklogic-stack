# docker-marklogic-stack

Docker MarkLogic Stack provides a set of docker services for running MarkLogic and services in the MarkLogic ecosystem. The following services are currently supported in the stack:

1. Marklogic server
1. Operational Data Hub
1. CoRB
1. MarkLogic Content Pump
1. Slush discovery app

## Service Details

### MarkLogic server (marklogic)

This service will remain running. The data files of MarkLogic are configured to be mounted to the host filesystem.
The volume mount allows state to be managed between sessions.

### Operational Data Hub (odh)

This service will remain running. The container working directory (default: `/usr/src`) is configured to be mounted to the host filesystem to maintain the configuration state. If you are starting a new ODH project, create the project in the container working directory so that you can access the configuration files on your host machine.

### MarkLogic Content Pump (mlcp)

This is an emphemeral service to run MarkLogic content pump. The root directory of this project is configured to mount to the container working directory.

### CoRB (corb)

This is an emphermeral service for running CoRB jobs.

### Slush Discovery App (slush)

This is a continuous service hosting the slush discovery app. 

# Getting Started

## Install Requirements

[Docker](https://docs.docker.com/install/) is the only tool you'll need to install.

## Setup

Confirm Docker engine is running and has access to the Docker repo.

`docker run hello-world`

Clone this repository.
`git clone https://github.com/ezbc/docker-marklogic-stack`

Change directories to the root of the project
`cd docker-marklogic-stack`

Pull images to host machine. This may take a few minutes.
`docker-compose pull`

Ensure the default ports used by the services are unused. 
If any of the following ports are in use change the service ports. 
See [Configuring the Environment](#configuring-the-environment) 
for details on changing the service ports.
1. 8080: used by the ODH
1. 8000-8020: used by MarkLogic
1. 3000: used by Slush
1. 9070: used by Slush

Start services
`docker-compose up marklogic`

You should see a log showing MarkLogic has started: "Starting MarkLogic: [  OK  ]" . 
To confirm MarkLogic server has started visit `http://localhost:8001`.

## Running Headless Services

CoRB, MLCP and ODH all have headless services. We will run CoRB and MLCP 
in new containers for each task. Since the ODH is already running to 
serve the ODH webapp, we will execute tasks inside the existing ODH container.

We will use two docker-compose commands to run service tasks, `exec` and 
`run`. `exec` will execute a command inside an existing container.
`run` will create a container, run the task, and tear down the container.

For example for an ingest into MarkLogic with MLCP, you would use the
`run` command to create a container, run the ingest,
and tear down the container after the ingest finished.

### Running Tasks in ODH Service

We can execute a gradle command in the ODH container with the following

`docker-compose exec odh gradle tasks`

You should see a list of gradle tasks available to the gradle client.

#### Working with Project Files

Project files in the ODH service container are mounted to the host machine.
An ODH volume is configured on the host machine mapped to the working 
directory on the ODH container.

To test the volume mount create a file from your host machine in
the ODH mounted directory (default `./odh`).

`touch ./odh/test_mount`

Within the container this should file should be at working directory.
Test the new file is visible to the odh container.

`docker-compose exec odh ls test_mount`

You should see the `test_mount` file returned. You are now able to make changes
to a gradle project and use the odh docker service to interact with MarkLogic server.

## Configuring the Environment

Configure the docker environment if needed. Open the `.env` file in your favorite text editor. 
Change the volume directories or the ports. 

### Service Configuration Details
|Service Name|Docker Service Name|Default Ports Used|
|------------|-------------------|------------------|
|MarkLogic Server|marklogic|8000-8020 |
|Operational Data Hub|odh|8080|
|Slush Discovery App|slush|9070,3000|
|CoRB|corb|none|
|MLCP|mlcp|none|
