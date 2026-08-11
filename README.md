# ICD API Local Docker Deployment

This setup allows you to host both ICD-11 and ICD-10 APIs locally using Docker Compose.

## Important Note regarding WHO Support
* **ICD-11**: Uses the **official** World Health Organization container (`whoicd/icd-api`).
* **ICD-10**: The WHO **does not provide** an official offline Docker container for ICD-10 (their local API container is exclusively for ICD-11). Therefore, this setup uses a popular community-maintained ICD-10 API container (`ghcr.io/dot-base/icd-10-api`).

## Prerequisites
* Docker and Docker Compose installed.

## Running the Services
To start the APIs, navigate to this directory and run:
```bash
docker-compose up -d
```

## Accessing the APIs

An Nginx reverse proxy serves both APIs on port `80` (and routes them appropriately).

* **ICD-11 API**: `http://localhost/icd11/`
    * The ICD-11 Coding Tool can be accessed at: `http://localhost/icd11/ct11`
    * The ICD-11 Browser can be accessed at: `http://localhost/icd11/browse11`
* **ICD-10 API**: `http://localhost/icd10/`
    * This connects to the community ICD-10 API running behind the proxy.

*Note: You can also access them directly on their mapped ports (ICD-11 on 8081, ICD-10 on 8082) if you prefer not to use the Nginx proxy on port 80.*
