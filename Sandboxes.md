## Comprehensive Guide to Using JOptTourOptimizer Spring Server and native JOpt with Docker and Sandboxes

<a href="https://dna-evolutions.com/" target="_blank"><img src="https://docs.dna-evolutions.com/indexres/dna-temp-logo.png" width="110"
title="DNA-Evolutions" alt="DNA-Evolutions"></a>

### Introduction
The JOptTourOptimizer suite offers a flexible routing optimization engine designed to solve complex tour-optimization problems. This powerful tool can handle various constraints such as time windows, skills, and mandatory requirements. This document provides a comprehensive guide on setting up and using the TourOptimizer locally via Docker, accessing various sandboxes, using the native Java library, and exploring an Angular sample application.

Please note that you can also use the sandboxes to access a JoptTourOptimizer that is NOT hosted locally.

### 1. Starting a Locally Running TourOptimizer Spring Server via Docker
To start the JOptTourOptimizer Spring Server locally using Docker, follow these steps:

1. **Pull the Docker Image:**
   ```sh
   docker pull dnaevolutions/jopt_touroptimizer:latest
   ```
2. **Run the Docker Container:**
   ```sh
   docker run -d -p 8080:8080 --name jopt_touroptimizer dnaevolutions/jopt_touroptimizer:latest
   ```
3. **Access the Swagger Interface:**
   Open your browser and navigate to `http://localhost:8080/swagger-ui.html` to interact with the API using the Swagger interface.

For more detailed instructions, refer to the [Docker-REST-TourOptimizer README](https://github.com/DNA-Evolutions/Docker-REST-TourOptimizer/blob/main/README.md). 

The JOptTourOptimizer also supports a fire and forget mode, allowing you to submit optimization requests without waiting for immediate results. This is particularly useful for handling large datasets or complex optimization scenarios. For more information on the fire and forget mode, refer to its [documentation](https://github.com/DNA-Evolutions/Docker-REST-TourOptimizer/blob/main/TourOptimizerWithDatabase.md).

### 2. Sandboxes for Accessing the Locally Running TourOptimizer Engine
You can use several sandboxes implemented via code-server and Docker to interact with the locally running TourOptimizer engine. These sandboxes provide an easy way to experiment with the optimizer without setting up a complete development environment.

Please note that each sandbox is a docker container and you need to connect to a local JOptTourOptimizer via the endpoint `http://host.docker.internal:8081` instead of ~`http://localhost:8081`~.


#### Python Sandbox
1. **Pull and Run the Python Sandbox:**
   ```sh
   docker run -it -d --name jopt-py-rest-examples -p 127.0.0.1:8033:8080 -v "$PWD/:/home/coder/project" dnaevolutions/jopt_py_example_server:latest
   ```
2. **Access the Sandbox:**
   Open your browser and go to `http://localhost:8033` and log in with the password `jopt`.

#### Java Sandbox
1. **Pull and Run the Java Sandbox:**
   ```sh
   docker run -it -d --name jopt-rest-examples -p 127.0.0.1:8043:8080 -v "$PWD/:/home/coder/project" dnaevolutions/jopt_rest_example_server:latest
   ```
2. **Access the Sandbox:**
   Open your browser and go to `http://localhost:8043` and log in with the password `joptrest`.

#### C# Sandbox
1. **Pull and Run the C# Sandbox:**
   ```sh
   docker run -it -d --name jopt-net-rest-examples -p 127.0.0.1:8023:8080 -v "$PWD/:/home/coder/project" dnaevolutions/jopt_net_example_server:latest
   ```
2. **Access the Sandbox:**
   Open your browser and go to `http://localhost:8023` and log in with the password `joptrest`.

For more details, visit the respective repositories:
- [Python REST Client Examples](https://github.com/DNA-Evolutions/Python-REST-Client-Examples?tab=readme-ov-file#use-our-sandbox-in-your-browser-docker-required)
- [Java REST Client Examples](https://github.com/DNA-Evolutions/Java-REST-Client-Examples?tab=readme-ov-file#use-our-sandbox-in-your-browser-docker-required)
- [C# REST Client Examples](https://github.com/DNA-Evolutions/C-Sharp-REST-Client-Examples?tab=readme-ov-file#use-our-sandbox-in-your-browser-docker-required)

### 3. Using the Native Java Library in a Sandbox Without REST
For those who prefer using the native Java library without REST, we provide a sandbox environment using Docker. This sandbox is based on code-server and includes the Java TourOptimizer examples.

1. **Pull and Run the Java Sandbox:**
   ```sh
   docker run -it -d --name jopt-examples -p 127.0.0.1:8042:8080 -v "$PWD/:/home/coder/project" dnaevolutions/jopt_example_server:latest
   ```
2. **Access the Sandbox:**
   Open your browser and go to `http://localhost:8042` and log in with the password `jopt`.

This environment allows you to use the Java TourOptimizer examples directly within your browser, leveraging the full capabilities of the JOptTourOptimizer library.

Refer to the [Java-TourOptimizer-Examples](https://github.com/DNA-Evolutions/Java-TourOptimizer-Examples/tree/master?tab=readme-ov-file#use-our-sandbox-in-your-browser-docker-required) for more information.

### 4. Angular Sample Application
We also provide an Angular sample application to demonstrate how to integrate the JOptTourOptimizer into a web application. This application serves as a practical example of using the optimizer's capabilities in a modern web framework.

For more details and to access the sample application, please visit the [Docker-REST-TourOptimizer repository](https://github.com/DNA-Evolutions/Docker-REST-TourOptimizer?tab=readme-ov-file#how-to-start-the-dna-demo-application).