# ICD API Docker Deployments

This repository provides Docker configurations to host both ICD-11 and ICD-10 APIs locally or on a server manager like Coolify. It also includes an **API Gateway Guard** to protect your endpoints from unauthorized access.

## Important Note regarding WHO Support
* **ICD-11**: Uses the **official** World Health Organization container (`whoicd/icd-api`).
* **ICD-10**: The WHO **does not provide** an official offline Docker container for ICD-10. Therefore, this setup uses a popular community-maintained ICD-10 API container (`ghcr.io/dot-base/icd-10-api:6.0.0`). The `6.0.0` tag is specifically used because it supports both `amd64` and `arm64` architectures natively.

---

## 🔒 The API Token Guard
Neither of these containers natively supports simple API token authentication. To secure them, this repository includes an Nginx `api-gateway`. 

All traffic must pass through this gateway. The gateway checks if a `?token=` parameter is present in the URL.
* If the token is missing or incorrect, it returns a `403 Forbidden`.
* If the token is correct, it routes the traffic to the appropriate ICD API based on the domain you requested.

**To change your secret token:**
Open `nginx.conf` and change the string `"mysecret"` in both `server` blocks to a secure password of your choosing.

---

## 🚀 Option 1: Deployment via Coolify

Because the API Gateway requires the external `nginx.conf` file, **you must deploy this stack in Coolify by connecting it to this Git repository** (you can no longer use the "Raw Text" deployment).

1. In your Coolify dashboard, create a new resource and choose **Docker Compose (from Git)** or link your repository directly.
2. Select the `coolify-compose.yml` file as your Compose file.
3. Click **Deploy**.
4. Once deployed, configure the domains in the Coolify UI:
   * Navigate to the **`api-gateway`** service.
   * Add **BOTH** of your domains here (e.g., `https://icd10.yourdomain.com, https://icd11.yourdomain.com`).
   * Make sure the internal port for the `api-gateway` is set to **`80`**.
   * *(Do not map any domains to the `icd10-api` or `icd11-api` services directly. They should remain hidden from the outside world.)*

*Note: The ICD-11 container requires significant memory to load the classification database. Ensure your server has at least 4GB to 8GB of free RAM to prevent out-of-memory (OOM) crashes.*

---

## 💻 Option 2: Local Deployment (Docker Compose)

To run this locally on your own machine or a standard VPS without Coolify:

1. Ensure Docker and Docker Compose are installed.
2. Navigate to this directory in your terminal.
3. Start the services:
   ```bash
   docker-compose up -d
   ```
4. The API Gateway will serve both APIs on port `80`. Ensure you append the token to your local requests (e.g., `http://localhost/11/ct11?token=mysecret`).

---

## 🧪 Testing Your Endpoints

Neither container serves a "homepage" at the root path (`/`). To verify your deployment is working, use the following test paths (don't forget your token!):

### ICD-11 Endpoints
The official WHO container includes graphical tools alongside the raw API.
* **Coding Tool**: `https://icd11.yourdomain.com/ct11?token=mysecret`
* **Browser**: `https://icd11.yourdomain.com/browse11?token=mysecret`
* **REST API Base Path**: `/icd/release/11/...?token=mysecret`

### ICD-10 Endpoints
The community ICD-10 container is a pure JSON API without a graphical interface.
* **Test Search API**: `https://icd10.yourdomain.com/api/icd10?search=cholera&token=mysecret`
*(This will return a JSON array of all ICD-10 codes related to cholera).*
