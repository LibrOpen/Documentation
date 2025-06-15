# Backend

## Introduction

We use AdonisJS as a backend framework. AdonisJS is a Node.js framework that provides a lot of features to build a web application. You can see the documentation [here](https://docs.adonisjs.com/guides/introduction).

As the repository is a monorepo, the backend is in the `apps/backend` folder. We also use pnpm as a package manager. The main difference between pnpm and npm is that pnpm uses a single storage for all packages, so you can save a lot of disk space.

## Setup

In order to start using our project, the `.env` file must be set up!
Use the `.env.example` file and complete it with the necessary keys (client keys, AWS keys...)

To start the backend, you should have Node.js installed on your machine.

Go to backend folder and install the web dependencies:

```
pnpm install
```

To build the project with the docker-compose at the root of the repository:

```
docker-compose up --build
```

Start the backend application (from the `apps/backend` folder):
```
pnpm run dev
```

## AdonisJS

LibrOpen backend uses AdonisJS. So you can use directly AdonisJS features.
Basically, the main command is

```
node ace
```

where you can see all commands

You can see the AdonisJS documentation [here](https://docs.adonisjs.com/guides/introduction)

## Seeding

The project uses AdonisJS's built-in seeder system to populate the database with initial data. Seeders are located in the `apps/backend/database/seeders` directory.

### Running Seeders

To run all seeders:
```
node ace db:seed
```

To run a specific seeder:
```
node ace db:seed --files="./database/seeders/user_seeder.ts"
```

### Creating a New Seeder

To create a new seeder:
```
node ace make:seeder name
```

## Route and query

If you want to create a new route, you should update the ```route``` file in the ```start``` folder.

You should add your query in the ```api``` group, and you can create your own group for your root, for example if your queries should be in the path like ```api/group_name/``` so you can create ```group_name``` group.

You can add some middleware, the basic middleware is the auth, where the user must be logged to execute this query.

After this, you can create a Controller file, where you can add your code to execute your query.

For this, I suggest you to see the AdonisJS documentation.