# Tutorial - Using Fire and Forget mode

<a href="https://dna-evolutions.com/" target="_blank"><img src="https://docs.dna-evolutions.com/indexres/dna-temp-logo.png" width="110"
title="DNA-Evolutions" alt="DNA-Evolutions"></a>


# Get in Touch

Should you need assistance or have questions, we're here to help. Please reach out to us through our company website at [www.dna-evolutions.com](https://www.dna-evolutions.com) or directly via email at [info@dna-evolutions.com](mailto:info@dna-evolutions.com).

---


## Overview

* [Introduction](#introduction)
* [Step 1: Establish a Docker Network](#step-1-establish-a-docker-network)
* [Step 2: Start a MongoDB Container with User Credentials](#step-2-start-a-mongodb-container-with-user-credentials)
* [Step 3: Connect Mongo Express](#step-3-connect-mongo-express)
* [Step 4: Launch TourOptimizer](#step-4-launch-touroptimizer)
* [Step 5: Run an Optimization and Save/Load the Result](#step-5-run-an-optimization-and-save-load-the-result)

---

## Introduction

The fire and forget mode in DNA's TourOptimizer is designed to handle optimization tasks efficiently while avoiding potential timeout issues that may arise during long-running optimizations. The RESTful API provides, for example, two endpoints, **run** and **runFAF**, to cater to different use cases:

1. **run**: This endpoint is used when you want to start an optimization and wait for it to complete. The client maintains a connection to the server until the optimization is finished or an error occurs. In scenarios where the optimization takes a long time to complete, this approach may lead to timeout exceptions, especially if the server or client have timeout constraints.

2. **runFAF** (Fire and Forget): This endpoint is designed to overcome the limitations of the "run" endpoint. When using the "runFAF" endpoint, the optimization process is started, and the connection between the client and server is terminated. The client immediately receives a response indicating whether the optimization has started successfully. This approach helps to avoid timeout exceptions that can occur during long-running optimizations.

The fire and forget mode allows you to efficiently manage resources and streamline the optimization process, particularly when working with large optimization problems or when running multiple optimizations simultaneously. The TourOptimizer saves the resulting snapshots to a MongoDB database, and you can easily retrieve the saved results using other provided endpoints when needed.

---

# Hands-on Tutorial: Setting Up a Local Fire and Forget TourOptimizer-Database Test Environment

This guide aims to help you initiate an optimization using DNA's TourOptimizer, store the results in a MongoDB, and retrieve them. For added convenience, we will also connect Mongo-Express to the database for easy data visualization.

---

# Step 1: Establish a Docker Network

To set up a Docker network that will later host MongoDB, Mongo Express, and TourOptimizer, execute the following command:

```bash
docker network create mongonetwork
```

---

## Step 2: Start a MongoDB Container with User Credentials

Use the command below to initiate a MongoDB container named ``dnamongo`` with user credentials:

```markdown
docker run -d --name dnamongo --network mongonetwork \
	-p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=dnauser \
	-e MONGO_INITDB_ROOT_PASSWORD=dnapwd mongo:latest
```

---

## Step 3: Connect Mongo Express

For a visual representation of the MongoDB data, connect Mongo Express to the network created. For additional information, refer to the [Mongo Express GitHub repository](https://github.com/mongo-express/mongo-express).

Execute the following command to attach Mongo Express to the ``dnamongo`` database container:

```bash
docker run -it --rm --name mongo-express \
	--network mongonetwork -p 8050:8081 \
	-e ME_CONFIG_MONGODB_URL="mongodb://dnauser:dnapwd@dnamongo:27017" \
	mongo-express:latest
```

After running the command, access Mongo Express by visiting [http://localhost:8050/](http://localhost:8050/) and ensure that your MongoDB database is operational.

---

## Step 4: Launch TourOptimizer

Start TourOptimizer with the appropriate database-related environment variables (via the `-e` argument):

1. ``DNA_DATABASE_ACTIVE``: TourOptimizer will try to connect to a MongoDB and activate the corresponding endpoints.
2. ``DNA_DATABASE_URI``: The URI for MongoDB, including login information and details about replica sets. Here, we connect to the ``dnamongo`` database container.
3. ``DNA_DATABASE_DB``: The database where the data will be stored.

Also, ensure to run the container inside the correct network (`--network mongonetwork`).

For a complete list of environment variables, refer to the TourOptimizer Docker Variables documentation (To be added...).


```bash
docker run --rm --name myJOptTourOptimizer \
	-e SPRING_PROFILES_ACTIVE="cors" \
	-e DNA_DATABASE_ACTIVE=true \
	-e DNA_DATABASE_URI="mongodb://dnauser:dnapwd@dnamongo:27017/admin"  \
	-e DNA_DATABASE_DB=touroptimizer \
	--network mongonetwork \
	-p 8081:8081 \
	dnaevolutions/jopt_touroptimizer:latest
```

Now, open [http://localhost:8081/](http://localhost:8081/) in your browser to verify if the TourOptimizer is operational. As we launched the container in "attached" mode, you will see an exception in your console if the TourOptimizer fails to connect to the database successfully.

Note: Ensure you're using a relatively new version of `jopt_touroptimizer` (at least version [1.2.1-X](https://hub.docker.com/r/dnaevolutions/jopt_touroptimizer/tags)).

---

## Step 5: Run an Optimization and Save/Load the Result

To save an optimization in Fire and Forget mode, it's essential to provide an `OptimizationPersistenceSetting` object that manages how the result is stored. This object is defined within the extension object, on the same level as the `keySetting` object.

### Understanding the OptimizationPersistenceSetting object

The `OptimizationPersistenceSetting` object is responsible for managing how the optimization results and associated data are stored in a MongoDB database. To make the most out of this object, set it up with the following configurations:

1. **MongoDB settings** (`MongoOptimizationPersistenceSetting`): Establish the database settings.
   - `enablePersistence`: Activates or deactivates database persistence.
   - `secret`: An optional secret key for encrypting the content of the final snapshot/result. If no encryption is required, leave it blank. Remember, metadata and stream data are always stored as decrypted plain text. If you lose the secret, the data cannot be retrieved.
   - `expiry`: Determines the time period after which the saved snapshot/result will be automatically deleted (for instance, "PT48H" denotes 48 hours).

2. **Optimization persistence strategy settings** (`OptimizationPersistenceStratgySetting`): Determines how optimization results are stored.
   - `saveConnections`: Stores element connections in the database. To conserve space, set this to false.
   - `saveOnlyResult`: When set to true, only the result object is stored, omitting additional data.

3. **Stream persistence strategy settings** (`StreamPersistenceStratgySetting`): Configures the storage of stream data like progress, status, warnings, and errors.
   - `saveProgress`, `cycleProgress`: Allows continuous saving and cycling of progress data.
   - `saveStatus`, `cycleStatus`: Enables continuous saving and cycling of status data.
   - `saveWarning`: Allows storage of warning data.
   - `saveError`: Enables storage of error data.

Create an `OptimizationPersistenceSetting` object with these settings and send it with your optimization request to manage how the optimization results and related data are stored. Here's a sample JSON configuration:

```json
"persistenceSetting": {
  "mongoSettings": {
    "enablePersistence": true,
    "secret": "",
    "expiry": "PT48H",
    "optimizationPersistenceStratgySetting": {
      "saveOnlyResult": false,
      "saveConnections": false
    },
    "streamPersistenceStratgySetting": {
      "saveProgress": true,
      "cycleProgress": true,
      "saveStatus": true,
      "cycleStatus": true,
      "saveWarning": true,
      "saveError": true
    }
  }
}
```

---

### Java client

We prepared three examples that you can find in our [Java-REST-Client-Examples](https://github.com/DNA-Evolutions/Java-REST-Client-Examples) repository.  XXX LINK

- `TourOptimizerFireAndForgetExample.java`: Demonstrates how to run an optimization and save it to the database.
- `TourOptimizerFireAndForgetSearchInDatabaseExample.java`: Shows how to search optimizations within a database and display their meta info.
- `TourOptimizerFireAndForgetLoadFromDatabaseExample.java`: Guides how to load an optimization from a database.

---

### C# Client (To be added...)

---

### Using a Browser

Visit [http://localhost:8081/](http://localhost:8081/) to access the swagger-ui of the TourOptimizer. 

From the menu, open and expand **runFAF**. To the right, click **Try it out**. You'll find the **Request body** (in JSON format). Initially, you might want to change the **ident** to `JOPT-Test`. 

At the bottom of the request body (you may need to scroll down), you'll see the `persistenceSetting` object. On the same level, you will also find the `creatorSetting` object. By default, the creator is `PUBLIC_CREATOR`. You can modify this name according to your needs. If you prefix the name with `hash:`, for example, `hash:PUBLIC_CREATOR`, the `sha-256` value of the string `PUBLIC_CREATOR` will be stored.

When you're ready, click **Execute** below the body. You should receive a response body promptly, indicating a successful start. 

Now, from the menu, open and expand **findsOptimizationInfos**. Click **Try it out** and modify the **Request body** to look as follows:

```json
{
  "creator": "PUBLIC_CREATOR"
}
```

(Please also explore the other parameters of the **DatabaseInfoSearch** model.)

You should receive a list of one or multiple optimization metadata. A single response item could look like this:

```json
{
	"creator":"PUBLIC_CREATOR",
	"ident":"JOPT-Test",
	"createdTimeStamp":1683722589222,
	"type":"OptimizationConfig<JSONConfig>",
	"contentType":"application/x-bzip2",
	"id":"645b915dc73a8250a670debd"
}
```

The `ident` is a user-defined string to help identify the run. The `id` is a unique identification string generated by the database. 

In the next step, we will extract an optimization (not just the metadata). From the menu, open and expand **findOptimization** and press **Try it out** and modify the **Request body** to look as follows (please adjust `id` and `creator` values to match the previous search result):

```json
{
  "creator": "PUBLIC_CREATOR",
  "id": "645b915dc73a8250a670debd"
}
```

Click **Execute** and you should receive the Optimization object in return.

---

## Agreement
For reading our license agreement and for further information about license plans, please visit <a href="https://www.dna-evolutions.com" target="_blank">www.dna-evolutions.com</a>.

--- 

## Authors
A product by [dna-evolutions ](https://www.dna-evolutions.com)&copy;
