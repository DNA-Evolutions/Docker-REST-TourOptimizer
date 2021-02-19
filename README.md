# Docker-REST-TourOptimizer

<a href="https://dna-evolutions.com/" target="_blank"><img src="https://docs.dna-evolutions.com/indexres/dna-temp-logo.png" width="110"
title="DNA-Evolutions" alt="DNA-Evolutions"></a>

Containerizing an application helps to use it more conveniently across different platforms and, most importantly, as a microservice. 
Further, scaling an application becomes more straightforward as different standardized orchestration tools can be utilized. It can be launched either (locally) as part of a docker-compose or as a highly-scalable web-micro-service in a Kubernetes cluster, to give an example.

---


## Tech Stack - How JOptTourOptimizer is containerized
RESTful JOptTourOptimizer can be used as a <a href="https://en.wikipedia.org/wiki/Docker_software" target="_blank">Docker</a> container utilizing <a href="https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html" target="_blank">Spring WebFlux</a> and <a href="https://swagger.io/" target="_blank">Swagger</a>. Internally the Java version of TourOptimizer is utilized.

![TourOptimizer-Docker-Integration](https://docs.dna-evolutions.com/rest/touroptimizer/res/touroptimizer-cloud-integration-highres.svg)

---

## How to start JOptTourOptimizer-Docker
JOptTourOptimizer is hosted on <a href="https://hub.docker.com/r/dnaevolutions/jopt_touroptimizer" target="_blank">Docker Hub</a>. In a first step, you can pull the image and start a container in your local docker environment, using, for example, <a href="https://docs.docker.com/desktop/" target="_blank">Docker Desktop</a>.

### Setup JOptTourOptimizer-Docker

Setting up JOptTourOptimizer in your Docker environment only takes these three steps:

**1) Pulling the image:**

```xml
docker pull dnaevolutions/jopt_touroptimizer
```

**2) Running a container:**

```xml
docker run -d --rm \
 	--name myJOptTourOptimizer \
 	-e SPRING_PROFILES_ACTIVE="cors" \
 	-p 8081:8081  \
 	dnaevolutions/jopt_touroptimizer
```


(Same command as a single line)

```xml
docker run -d --rm  --name myJOptTourOptimizer -e SPRING_PROFILES_ACTIVE="cors" -p 8081:8081  dnaevolutions/jopt_touroptimizer
```

Activating the profile "cors" will allow doing REST-calls from the same localhost from another application.

(If desired, please adjust <a href="https://docs.docker.com/engine/reference/run/" target="_blank">docker run argument</a> to your needs)


**3) Open:** <a href="http://localhost:8081" target="_blank">http://localhost:8081</a>

---

## How to make use of JOptTourOptimizer-Docker

By default, you are allowed to run an Optimization with up to 15 elements without providing a license key. In case you already have a license key for JOptTourOptimizer (Java-Maven) you can use that one.

After you opened <a href="http://localhost:8081" target="_blank">http://localhost:8081</a> you see the Swagger interface of JOptTourOptimizer. You can generate a client in your desired language by using the <a href="https://editor.swagger.io/" target="_blank">SwaggerEditor</a>.

Simply copy the Swagger definition under <a href="http://localhost:8081/v3/api-docs" target="_blank">http://localhost:8081/v3/api-docs</a> into the <a href="https://editor.swagger.io/" target="_blank">SwaggerEditor</a> and accept to convert JSON to YAML.

!!! info
	So far, we have gradually started to "open up" features from the Java Core library for the Swagger frontend. However, each feature needs to be tested and evaluated for its added value.

---

## DNA Demo Application

To utilize JOptTourOptimizer-Docker, we created an angular-demo application. This demo application is hosted on <a href="https://azure.microsoft.com/" target="_blank">Microsoft Azure</a> and is made available via <a href="https://demo.dna-evolutions.com/" target="_blank">https://demo.dna-evolutions.com</a>. 

!!! info
	In the future, we are planning to launch this demo application as an open-source project. Please feel free to contact us in case you already like to get the latest source-code.

The angular-demo application was generated with <a href="https://github.com/angular/angular-cli" target="_blank">Angular CLI version 10.1.2</a>. Further, we utilized <a href="https://github.com/OpenAPITools/openapi-generator" target="_blank">OpenApi Generator</a> to automatically create the required TypeScript models from the OpenApi definition provided from our Swagger definition of JOptTourOptimizer.

---

## How to start the DNA Demo Application

The demo application is also available on <a href="https://hub.docker.com/r/dnaevolutions/jopt_demoapplication" target="_blank">Docker Hub</a>. There are multiple ways on how you can attach the demo application to JOptTourOptimizer-Docker.

For example:

**A)** Let the demo container directly access the localhost (<a href="https://de.wikipedia.org/wiki/Cross-Origin_Resource_Sharing" target="_blank">CORS</a> eventually needs to be enabled for the browser).

**B)** Using a docker-compose with both containers. Only expose the demo application if desired.

**C)** Attaching the demo container to the same network.

**...**

### Setup the Anguar DNA Demo Application

In this walkthrough, we let the demo application access the localhost as in **A)**. Next, you need to launch JOptTourOptimizer-Docker in a container as described in the JOptTourOptimizer setup. The page <a href ="http://localhost:8081 " target ="_blank ">http://localhost:8081</a> needs to be accessible. Further, the container needs to be started with the profile "cors" as described above.

**1) Pulling the image:**

```xml
docker pull dnaevolutions/jopt_demoapplication
```

**2) Running a container:**

```xml
docker run -d --rm \
	--name myJOptTourOptimizerDemo \
	-p 3000:80 \
	-v ${PWD}:/usr/src/app \
	-e JOPT_SWAGGER_HOST="http://localhost" \
	-e JOPT_SWAGGER_PORT="8081" \
	dnaevolutions/jopt_demoapplication
```


(Same command as a single line)

```xml
docker run -d --rm --name myJOptTourOptimizerDemo -p 3000:80 -v ${PWD}:/usr/src/app -e JOPT_SWAGGER_HOST="http://localhost" -e JOPT_SWAGGER_PORT="8081" dnaevolutions/jopt_demoapplication
```

(If desired, please adjust <a href="https://docs.docker.com/engine/reference/run/" target="_blank">docker run argument</a> to your needs)


**3) Open:** <a href="http://localhost:3000" target="_blank">http://localhost:3000</a>

...and you should see the DNA-Demo-Application.


---

## Agreement
For reading our license agreement and for further information about license plans, please visit <a href="https://www.dna-evolutions.com" target="_blank">www.dna-evolutions.com</a>.

--- 

## Authors
A product by [dna-evolutions ](https://www.dna-evolutions.com)&copy;




