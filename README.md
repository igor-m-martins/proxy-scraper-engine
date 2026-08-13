# Scraped Relay & Anti-Bot System

A robust, asynchronous web scraping architecture designed to bypass strict anti-bot mechanisms (such as Cloudflare) and safely extract data using a decoupled Relay/Proxy pattern. 

---

## Architecture Overview

```text
+------------------------+       POST /scrape      +------------------------+
|                        | --------------------->  |                        |
|   Client / Dashboard   |                         |   Main API (Flask)     |
|   (HTML / JavaScript)  | <---------------------  |   (Async Worker Pool)  |
+------------------------+       Job Status        +------------------------+
                                                              |
                                                       POST /api/v1/dados
                                                       (Secured via X-API-Key)
                                                              |
                                                              v
                                                   +------------------------+
                                                   |      Relay Proxy       |
                                                   |     (SSRF Protected)   |
                                                   +------------------------+
                                                              |
                                                       POST /v1 (JSON)
                                                              |
                                                              v
                                                   +------------------------+
                                                   |      FlareSolverr      |
                                                   |  (Headless Browser /   |
                                                   |   Anti-Bot Solver)     |
                                                   +------------------------+
                                                              |
                                                              v
                                                   +------------------------+
                                                   |    Target Websites     |
                                                   |  (Cloudflare / Bot-    |
                                                   |   Protected Targets)   |
                                                   +------------------------+
```

## 🚀 How It Works

### 1. Job Queuing & Asynchronous Processing
*   **Requests:** The client sends target URLs (up to 3 items in the free demo) to the main API via a **POST request**.
*   **Non-Blocking:** The API immediately responds with a **202 Accepted** status and a unique `job_id`, spinning up a background thread to prevent request blocking.
*   **Polling:** The client monitors the status endpoint using an asynchronous loop until the task is complete.

### 2. Decoupled Relay & Anti-Bot Bypass
*   **Anti-Blocking:** To avoid triggers like CAPTCHAs or HTTP 403/503 errors, the worker delegates requests to a dedicated **Relay Proxy Server**.
*   **FlareSolverr Integration:** The Relay interfaces with **FlareSolverr**, a headless browser proxy that automatically solves JavaScript challenges and validates cookies. This keeps the main application lightweight, avoiding the heavy overhead of local browser drivers like Selenium.

### 3. Security Measures
*   **API Key Authentication:** Communication between the main API and the Relay layer is cryptographically restricted via custom HTTP headers (`X-API-Key`) to prevent unauthorized third-party usage.
*   **SSRF Protection:** The Relay server validates incoming target hostnames before dispatching. It resolves destination IPs and actively blocks requests targeting loopback addresses (`127.0.0.1`), private subnets (`10.x.x.x`, `192.168.x.x`), and link-local ranges to secure the infrastructure.

---

## ✨ Features
*   **Anti-Bot Resiliency:** Seamlessly handles sites protected by JS challenges and Cloudflare layers.
*   **Background Execution:** Non-blocking thread pool for handling multiple target links concurrently.
*   **Data Extraction:** Parses page titles, contact emails, phone numbers, and social media handles using optimized Regular Expressions and **BeautifulSoup4**.
*   **Export Capabilities:** Interactive dashboard supporting immediate data conversion and file downloads into **JSON**, **CSV**, and **SQL** formats.

---

## 🛠️ Tech Stack

| Component | Technology |
| :--- | :--- |
| **Backend** | Python, Flask, Requests, BeautifulSoup4 |
| **Anti-Bot Engine** | FlareSolverr / Headless Browser automation |
| **Frontend** | HTML5, CSS3, Asynchronous JavaScript (Fetch API / Polling) |
| **Deployment** | PythonAnywhere (Main API) & Decoupled Relay Host |
