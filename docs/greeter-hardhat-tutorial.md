---
id: greeter-hardhat-tutorial
title: Greeter Envio indexer tutorial using Hardhat
sidebar_label: Greeter Contract Tutorial
slug: /greeter-hardhat-tutorial
---


This tutorial will take you through a step by step guide to building, deploying and indexing a Greeter smart contract using Envio and Hardhat. 

The final code for this tutorial can be seen **<em>[here](https://github.com/Float-Capital/envio-greeter-tutorial/)</em>**


<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->

- [Background](#background)
  * [Greeter contract](#greeter-contract)
  * [Hardhat](#hardhat)
  * [Envio](#envio)
- [Pre-requisites](#pre-requisites)
  * [Environment tooling](#environment-tooling)
  * [Install Envio](#install-envio)
- [Step by step instructions](#step-by-step-instructions)
  * [1. Create the project folders](#1-create-the-project-folders)
  * [2. Initialize the indexer](#2-initialize-the-indexer)
  * [3. Run our docker containers for local development](#3-run-our-docker-containers-for-local-development)
  * [4. Clone the contracts repository](#4-clone-the-contracts-repository)
  * [5. Deploy the contracts](#5-deploy-the-contracts)
  * [6. Start indexing!](#6-start-indexing)
  * [A couple extras](#a-couple-extras)

<!-- TOC end -->

## Background

### [Greeter contract](https://github.com/Float-Capital/hardhat-template)

The Greeter contract is a very simple smart contract that allows user to write a greeting message on the blockchain.

### [Hardhat](https://hardhat.org/)

Hardhat is a development toolkit for smart contracts. In this tutorial we will be using it to deploy and interact with our smart contracts.

### [Envio](https://envio.dev)

Envio is a framework for developing a backend to index and aggregate blockchain data into a graphQL query-able database. 

## Pre-requisites

### Environment tooling

1. [<ins>Node.js</ins>](https://nodejs.org/en/download/current) we recommend using something like [fnm](https://github.com/Schniz/fnm) or [nvm](https://github.com/nvm-sh/nvm) to install Node
1. [<ins>pnpm</ins>](https://pnpm.io/installation)
1. [<ins>Docker Desktop</ins>](https://www.docker.com/products/docker-desktop/)

### Install Envio
```bash
npm i -g envio
```

## Step by step instructions

First we will create the project folders, then initialize the indexer, then run our docker containers for local development, then clone the contracts repository, then deploy the contracts and finally start indexing!

### 1. Create the project folders

```bash
mkdir envio-greeter-tutorial
```
```bash
cd envio-greeter-tutorial
```
```bash
mkdir envio-indexer
```
```bash
cd envio-indexer
```

### 2. Initialize the indexer

```bash
npx envio init
```
Select based on the prompts
```bash
> Greeter
> Javascript
```

### 3. Run our docker containers for local development

> Dev note: 📢 make sure you have docker open

```bash
docker-compose up -d
```

> Dev note: 📢 run `docker-compose down -v` if you have stale containers running already

### 4. Clone the contracts repository

First lets move back to the root of our project directory
```bash
cd ..
```

```bash
git clone https://github.com/Float-Capital/hardhat-template.git
```
```bash
cd hardhat-template
```

### 5. Deploy the contracts

```bash 
pnpm i
```
```bash
cp .env.example .env
```
```bash
pnpm hardhat deploy
```
```bash
pnpm hardhat task:setGreeting --account "1" --greeting "Hola"
```
```bash
pnpm hardhat task:setGreeting --account "2" --greeting "gm"
```

> Dev note: 📢 run `rm -r -f deployments` to delete prior deployment data if you are re-deploying the contracts at a later point.

> Dev note: 📢 Check that the address of the deployment aligns with the address of smart contract in the `config.yaml` file, and if not, make sure they are the same.

### 6. Start indexing!

First lets get back to the indexer directory
```bash
cd ../envio-indexer
```
 
`codegen` will auto-generate all the indexing files, based on the setup files (config.yaml, schema.graphql, EventHandler.js)
```bash
npx envio codegen 
```
`start` will run the indexer and index events based on the rules set in the src/EventHandler.js file 
```bash
pnpm start 
```

> All indexing files are written in Rescript, and they **do not** need to be modified to run the indexer.

### A couple extras

Register the tables with hasura and open the dashboard to view the data
```bash
./generated/register_tables_with_hasura.sh
```
```bash
open http://localhost:8080
```

The hasura admin-secret is 'testing' and the tables can be viewed in the 'data' tab or queried from the playground


You can run additional tasks from the `hardhat-template` directory to see the indexer index live events
```bash
pnpm hardhat task:setGreeting --account "2" --greeting "realtime indexing"
```