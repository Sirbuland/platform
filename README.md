# Huly Platform

[![X (formerly Twitter) Follow](https://img.shields.io/twitter/follow/huly_io?style=for-the-badge)](https://x.com/huly_io)
![GitHub License](https://img.shields.io/github/license/hcengineering/platform?style=for-the-badge)

⭐️ Your star shines on us. Star us on GitHub!

## About

The Huly Platform is a robust framework designed to accelerate the development of business applications, such as CRM systems. 
This repository includes several applications, such as Chat, Project Management, CRM, HRM, and ATS. 
Various teams are building products on top of the Platform, including [Huly](https://huly.io) and [TraceX](https://tracex.co).

![Huly](https://huly.io/_astro/dark-kanban.D2kAOX88_q4nnu.webp)

## Activity

![Alt](https://repobeats.axiom.co/api/embed/c42c99e21691fa60ea61b5cdf11c2e0647621534.svg "Repobeats analytics image")

## Self-Hosting

If you're primarily interested in self-hosting Huly without the intention to modify or contribute to its development, please use [huly-selfhost](https://github.com/hcengineering/huly-selfhost). 
This project offers a convenient method to host Huly using `docker`, designed for ease of use and quick setup. Explore this option to effortlessly enjoy Huly on your own server.

## Table of Content

- [Pre-requisites](#pre-requisites)
- [Fast start](#fast-start)
- [Installation](#installation)
- [Build and run](#build-and-run)
- Development mode
  - [Run in development mode](#run-in-development-mode)
  - [Update project structure and database](#update-project-structure-and-database)
  - Tests
    - [Tests](#tests)
    - [Unit tests](#unit-tests)
    - [UI tests](#ui-tests)
  - [Package publishing](#package-publishing)

## Pre-requisites

- Make sure you have the following installed on your system:
  - [Node.js](https://nodejs.org/en/download/) (v20 is required)
  - [Docker](https://docs.docker.com/get-docker/)
  - [Docker Compose](https://docs.docker.com/compose/install/)
- Make sure what docker and `docker compose` commands are available in your terminal (e.g. `docker --version` and `docker compose --version`).
- Make sure what docker and `docker compose` commands can be executed without sudo (e.g. `docker run hello-world` and `docker compose --version`).

## Installing Node.js:
Node.js is a JavaScript runtime environment enabling the execution of JavaScript code outside of a web browser.

## Using Node Version Manager (nvm):
- Utilizing Node Version Manager (nvm):
- Begin by installing [nvm](https://github.com/nvm-sh/nvm#installing-and-updating). Instructions for installation can be found
- After successfully installing nvm, proceed to your project directory.
- Execute the following command to utilize the version of Node.js defined in the project's .nvmrc file:

```bash
nvm use
```

## Manual Installation:
- Begin by downloading the most recent version from the [official website](https://nodejs.org/en/download/).
- Proceed by executing the installer and adhering to the provided instructions.

Once you've installed it, open your terminal or command prompt and type `$ node` to confirm that the installation was successful.

## Installing Docker:
Docker, a platform designed for the development, delivery, and execution of applications within containers.

## On Linux:
When working with a Linux environment, consult the [Docker documentation](https://docs.docker.com/engine/install/) for detailed steps on installing Docker Engine, specifically tailored to your Linux distribution.


## Verifying Installations:
Upon completion of the installation process, you can confirm the successful installation of Node.js, Docker by executing the following commands:

```bash
node -v
npm -v
docker --version
```

## Fast start

```bash
sh ./scripts/fast-start.sh
```

## Installation

You need Microsoft's [rush](https://rushjs.io) to install application.

Install [rush](https://rushjs.io) with `$ npm install -g @microsoft/rush` command and run `$ rush install` from the repository root, followed by `$ rush build` or just:

```bash
sh ./scripts/presetup-rush.sh
```

## Build and run

Development environment setup requires Docker to be installed on system.

Support is available for both amd64 and arm64 containers on Linux and macOS.

```bash
cd ./dev/
rush build    # Will build all the required packages. 
# rush rebuild  # could be used to omit build cache.
rush bundle   # Will prepare bundles.
rush package  # Will build all webpack packages.
rush validate # Will validate all sources with typescript and generate d.ts files required for ts-node execution.
rush svelte-check # Optional. svelte files validation using svelte-check.
rush docker:build   # Will build Docker containers for all applications in the local Docker environment.
rush docker:up # Will set up all the containers
```

Be aware `rush docker:build` will automatically execute all required phases like build, bundle, package.

or just:

```bash
sh ./scripts/build.sh
```

By default, Docker volumes named dev_db, dev_elastic, and dev_files will be created for the MongoDB, Elasticsearch, and MinIO instances.

Before you can begin, you need to create a workspace and an account and associate it with the workspace.

```bash
cd ./tool # dev/tool in the repository root
rushx run-local create-workspace ws1 -w DevWorkspace # Create workspace
rushx run-local create-account user1 -p 1234 -f John -l Appleseed # Create account
rushx run-local configure ws1 --list --enable '*' # Enable all modules, even if they are not yet intended to be used by a wide audience.
rushx run-local assign-workspace user1 ws1 # Assign workspace to user.
rushx run-local confirm-email user1 # To allow the creation of additional test workspaces.

```

or just:

```bash
sh ./scripts/create-workspace.sh
```

Accessing the URL http://localhost:8087 will lead you to the app in production mode.

Limitations:

- Local installation does not allow sending emails, so password recovery and notification to email functionalities are not working.
- Integrations with Telegram, Gmail, and other content sources are available only as Docker containers, built from private repository sources. However, these integrations can be used with the platform.

## Run in development mode

Development mode allows for live reloading and a smoother development process.

```bash
cd dev/prod
rushx dev-server
```

Then go to http://localhost:8080

Use the following login credentials:

```plain
Email: user1
Password: 1234
Workspace: ws1
```

## Update project structure and database

If the project's structure is updated, it may be necessary to relink and rebuild the projects.

```bash
rush update
rush build
```

It may also be necessary to upgrade the running database.

```bash
cd ./dev/tool
rushx upgrade -f
```

## Troubleshooting

If a build fails, but the code is correct, try to delete the [build cache](https://rushjs.io/pages/maintainer/build_cache/)

```bash
# from the project root
rm -rf common/temp/build-cache
```

and retry.

## Build & Watch

For development purpose `rush build:watch` action could be used.

It includes build and validate phases in watch mode.

## Tests

### Unit tests

```bash
rush test # To execute all tests

rushx test # For individual test execution inside a package directory
```

### UI tests

```bash
cd ./tests
rush build
rush bundle
rush docker:build
## creates test Docker containers and sets up test database
./prepare.sh
## runs UI tests
rushx uitest
```

To execute tests in the development environment, please follow these steps:

```bash
cd ./tests
./create-local.sh ## use ./restore-local.sh if you only want to restore the workspace to a predefined initial state for sanity.
cd ./sanity
rushx dev-uitest # To execute all tests against the development environment.
rushx dev-debug -g 'pattern' # To execute tests in debug mode with only the matching test pattern.
```

## Package publishing

```bash
node ./common/scripts/bump.js -p projectName
```

## Additional testing

This project is tested with BrowserStack.

<sub><sup>&copy; 2024 Hardcore Engineering Inc.</sup></sub>
