<p align="center">
  <img src="https://www.dna-evolutions.com/images/dna_logo.png" width="200" alt="DNA Evolutions Logo">
</p>

<h1 align="center">JOpt.TourOptimizer REST Server</h1>

<p align="center">
  <strong>Route optimization, scheduling, and resource planning as a reactive REST service.</strong>
</p>

<p align="center">
  <a href="https://www.dna-evolutions.com/docs/getting-started/home/introduction">Documentation</a> · 
  <a href="https://www.dna-evolutions.com/docs/learn-and-explore/rest/rest-server-touroptimizer">Server Guide</a> · 
  <a href="https://www.dna-evolutions.com/docs/learn-and-explore/rest/rest_client_touroptimizer">REST Clients</a> · 
  <a href="https://www.dna-evolutions.com/docs/getting-started/quickstart/jopt-ai-assistant">AI Assistant</a> · 
  <a href="https://demo.dna-evolutions.com">Live Demo</a> · 
  <a href="https://www.dna-evolutions.com/contact/">Contact</a>
</p>

<p align="center">
  <a href="https://hub.docker.com/r/dnaevolutions/jopt_touroptimizer"><img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/dnaevolutions/jopt_touroptimizer?style=flat-square&color=8A9219"></a>
  <a href="https://www.dna-evolutions.com"><img alt="Website" src="https://img.shields.io/badge/website-dna--evolutions.com-8A9219?style=flat-square"></a>
  <a href="https://www.linkedin.com/company/dna-evolutions/"><img alt="LinkedIn" src="https://img.shields.io/badge/LinkedIn-DNA_Evolutions-8A9219?style=flat-square&logo=linkedin"></a>
</p>

---

## What is this?

JOpt.TourOptimizer is a backend optimization engine for vehicle routing, field service scheduling, and resource planning. This repository provides the **containerized REST server**: a reactive Spring WebFlux application that exposes the full optimizer via REST endpoints with an OpenAPI 3 contract (Swagger UI).

You send a JSON optimization request. The server returns an optimized plan with routes, schedules, cost breakdowns, and violation details.

**This is a pre-built Docker image.** You do not need to compile anything. Pull the image, start the container, and call the API.


---

## Quick start

```bash
docker run -d --rm --name jopt-touroptimizer \
  -p 8081:8081 \
  -e SPRING_PROFILES_ACTIVE=cors \
  dnaevolutions/jopt_touroptimizer:latest
```

**Swagger UI:** [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html)  
**OpenAPI JSON:** [http://localhost:8081/v3/api-docs](http://localhost:8081/v3/api-docs)

That is all you need to start evaluating. Paste a JSON payload into the Swagger UI and run an optimization.

> **Free mode:** Up to 20 elements (nodes + resources) without a license. Extended evaluation (37 elements) with a free [sign-up](https://www.dna-evolutions.com/docs/learn-and-explore/feature-guides/license#free-license).

For full deployment documentation (including database mode, Kubernetes, Terraform, and platform-specific guides), see the [Server Guide](https://www.dna-evolutions.com/docs/learn-and-explore/rest/rest-server-touroptimizer).


---

## Full start (with database for fire-and-forget mode)

For asynchronous job submission, persistence, and webhook-based result delivery:

```bash
# Create a network
docker network create mongonetwork

# Start MongoDB
docker run -d --name dnamongo --network mongonetwork \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=dnauser \
  -e MONGO_INITDB_ROOT_PASSWORD=dnapwd \
  mongo:latest

# Start TourOptimizer with all features
docker run --rm --name jopt-touroptimizer \
  --network mongonetwork \
  -p 8081:8081 \
  -e SPRING_PROFILES_ACTIVE=cors \
  -e DNA_DATABASE_ACTIVE=true \
  -e DNA_DATABASE_URI="mongodb://dnauser:dnapwd@dnamongo:27017/admin" \
  -e DNA_DATABASE_DB=touroptimizer \
  -e DNA_SHOW_SYNCH_CONTROLLERS=true \
  -e DNA_DATABASE_FREE_SEARCH_ENABLED=true \
  -e DNA_DATABASE_JOB_IMPORT_ENABLED=true \
  -e DNA_WEBHOOK_VALIDATION=relaxed \
  dnaevolutions/jopt_touroptimizer:latest
```

For details on fire-and-forget mode, see the [FAF documentation](https://www.dna-evolutions.com/docs/learn-and-explore/rest/touroptimizer-faf).


---

## Architecture

| Property | Value |
|---|---|
| **Runtime** | Java 17, Spring Boot 3.4.x |
| **HTTP stack** | Spring WebFlux (reactive, non-blocking) |
| **API** | REST + OpenAPI 3.0 (Swagger UI) + SSE streaming |
| **State** | Stateless core. Optional MongoDB for async jobs. |
| **Scaling** | Horizontal. No sticky sessions. No shared in-memory state. |
| **Compute profile** | CPU-bound optimization, memory-intensive problem graphs |
| **Image** | Pre-built. No build tooling required. |

The server exposes synchronous endpoints (request, wait, result) and asynchronous fire-and-forget endpoints (submit job, poll or receive webhook). Progress streaming is available via Server-Sent Events.


---

## REST client libraries

OpenAPI-generated client libraries are available for multiple languages:

| Language | Repository |
|---|---|
| **Java** | [Java REST Client Examples](https://github.com/DNA-Evolutions/Java-REST-Client-Examples) |
| **Python** | [Python REST Client Examples](https://github.com/DNA-Evolutions/Python-REST-Client-Examples) |
| **C# / .NET** | [C# REST Client Examples](https://github.com/DNA-Evolutions/C-Sharp-REST-Client-Examples) |
| **TypeScript / Angular** | [Angular Demo Application](https://github.com/DNA-Evolutions/Angular-Demo-Application-Source) |

All clients connect to this server. For client setup instructions, see the [REST Client documentation](https://www.dna-evolutions.com/docs/learn-and-explore/rest/rest_client_touroptimizer).

> **Docker networking note:** If your client runs inside a container and the server runs on the host, use `http://host.docker.internal:8081` instead of `http://localhost:8081`.


---

## Docker sandboxes (browser-based evaluation)

If you want to explore JOpt in a browser IDE without any local toolchain, Docker sandboxes are available:

| Sandbox | Image | Port | Password |
|---|---|---|---|
| Java SDK Examples | `dnaevolutions/jopt_example_server:latest` | 8042 | `jopt` |
| Java REST Client | `dnaevolutions/jopt_rest_example_server:latest` | 8043 | `joptrest` |
| Python REST Client | `dnaevolutions/jopt_py_example_server:latest` | 8033 | `jopt` |
| C# REST Client | `dnaevolutions/jopt_net_example_server:latest` | 8023 | `joptrest` |

Example:
```bash
docker run -it -d --name jopt-examples \
  -p 127.0.0.1:8042:8080 \
  dnaevolutions/jopt_example_server:latest
```

Open [http://localhost:8042](http://localhost:8042) in your browser. For details, see the [Sandboxes documentation](https://www.dna-evolutions.com/docs/learn-and-explore/feature-guides/jopt-sandboxes).


---

## Key features

JOpt.TourOptimizer supports a comprehensive set of routing and scheduling features. For the full list with documentation links, see the [Feature List](https://www.dna-evolutions.com/docs/learn-and-explore/features/featurelist) and [Special Features](https://www.dna-evolutions.com/docs/learn-and-explore/special/special_features).

Highlights:

- **Pillar Nodes:** guaranteed SLA matching by architecture, not by penalty costs
- **AutoFilter:** multi-solution infeasibility management with transparent exclusion reporting
- **Manufacturing Planning:** dynamic production quantities as optimizer-controlled planning variables
- **ZoneCodes:** per-shift territory switching with multi-zone node membership
- **Zone Crossing Penalization:** asymmetric directional costs for bridge/tunnel/toll boundaries
- **Pickup and Delivery:** SimpleLoads, FlexLoads, TimedLoads, split delivery optimization
- **Overnight Stay:** multi-day routing with policy thresholds and recovery rules
- **FlexTime:** flexible shift starts (positive and negative) with labor policy controls
- **Open Assessor:** custom business rules injected via a stable, minimal interface
- **Reactive Events:** 9 typed RxJava 3 event streams, native Spring WebFlux integration


---

## Deployment guides

For production deployment on different platforms:

- [Linux](https://www.dna-evolutions.com/docs/system-integration/containerized-deployment/jopt-container-linux)
- [Windows](https://www.dna-evolutions.com/docs/system-integration/containerized-deployment/jopt-container-win)
- [macOS](https://www.dna-evolutions.com/docs/system-integration/containerized-deployment/jopt-container-macos)
- [Kubernetes](https://www.dna-evolutions.com/docs/system-integration/containerized-deployment/kubertnetes)
- [Terraform / Enterprise](https://www.dna-evolutions.com/docs/system-integration/containerized-deployment/terraform)


---

## Licensing

| Mode | Limit | Requirement |
|---|---|---|
| Free | 20 elements | None |
| Evaluation | 37 elements | Free [sign-up](https://www.dna-evolutions.com/docs/learn-and-explore/feature-guides/license#free-license) |
| Production | Unlimited | License key ([contact us](https://www.dna-evolutions.com/contact/)) |

License documentation: [License feature guide](https://www.dna-evolutions.com/docs/learn-and-explore/feature-guides/license)


---

## Further resources

| Resource | Link |
|---|---|
| Documentation Hub | [dna-evolutions.com/docs](https://www.dna-evolutions.com/docs/getting-started/home/introduction) |
| AI Assistant (GPT) | [JOpt AI Assistant](https://www.dna-evolutions.com/docs/getting-started/quickstart/jopt-ai-assistant) |
| Java SDK Examples | [GitHub](https://github.com/DNA-Evolutions/Java-TourOptimizer-Examples) |
| Live Demo | [demo.dna-evolutions.com](https://demo.dna-evolutions.com) |
| OpenAPI Schema | [swagger.dna-evolutions.com](https://swagger.dna-evolutions.com/v3/api-docs/OptimizeConfig) |
| Public JavaDoc | [public.javadoc.dna-evolutions.com](https://public.javadoc.dna-evolutions.com) |
| FAQ | [FAQ](https://www.dna-evolutions.com/docs/learn-and-explore/features/faq) |
| YouTube | [DNA Evolutions Channel](https://www.youtube.com/channel/UCzfZjJLp5Rrk7U2UKsOf8Fw) |


---

## Agreement

For license agreements and further information about license plans, please visit [www.dna-evolutions.com](https://www.dna-evolutions.com).


---

<p align="center">
  <strong>DNA Evolutions GmbH</strong><br>
  <a href="https://www.dna-evolutions.com">Website</a> · 
  <a href="mailto:info@dna-evolutions.com">info@dna-evolutions.com</a> · 
  <a href="https://www.linkedin.com/company/dna-evolutions/">LinkedIn</a> · 
  <a href="https://github.com/DNA-Evolutions">GitHub</a>
</p>
