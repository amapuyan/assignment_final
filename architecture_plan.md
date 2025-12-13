# Architecture & Implementation Plan

This document describes how the proposed cloud architecture for the **Mental Health Monitoring via Mobile Check-In App** will be implemented on Azure. It explains which services are used, how they connect to each other, and how the design aligns with concepts and assignments from the course.

---

## 1. Service Mapping


| Layer           | Service (Cloud)                            | Role in Solution                                                                                                    | Related Assignment / Module                                |
|----------------|---------------------------------------------|---------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Storage        | **Azure Blob Storage**                      | Stores raw daily mood check-in events and sleep data files (JSON/CSV) from the mobile app and simulated wearables. | Assignment 3: Cloud Storage & Data Lakes       |
| Compute (API)  | **Flask App on Azure App Service** or **Azure Container Apps** | Hosts the Flask REST API for receiving check-ins and serves the clinician dashboard web UI.                         | Assignment 2: (Flask)  |
| Compute (ETL)  | **Azure Functions (Timer-triggered)**       | Periodically reads raw data from Blob, cleans/aggregates it, and computes daily summaries and risk flags.           | Assignment 5: Containers & Cloud Run                     |
| Database / SQL | **Azure SQL Database** (or Azure Database for MySQL) | Stores patient registry, daily summary tables, and risk flags used for dashboards and analytics.                    | Assignment 7: Big Data & Analytics / Assignment 4: Cloud Databases & SQL     |
| Analytics / AI | **Azure ML / Notebook in Azure ML Studio**  | Optionally used to explore historical data and experiment with simple risk-scoring models, writing scores back to SQL. | Module 9: (AI)                        |

---

## 2. Data Flow Narrative (End-to-End)

Below is the end-to-end flow of data through the system, from patient interaction to clinician dashboard and analytics.

1. **Daily Patient Check-In (Frontend → API)**  
   - Each day, the patient opens the mobile app and completes a short mood check-in, including mood score, anxiety score, energy level, and stress level.  
   - The app sends this data as a JSON payload (e.g., `POST /api/checkin`) to the **Flask REST API** hosted on **Azure App Service** or **Azure Container Apps**.

2. **Initial Storage of Raw Data (API → Storage / DB)**  
   - The Flask API first validates the incoming payload (required fields, ranges, etc.).  
   - It then performs two writes:
     - Appends the raw event (JSON/CSV) into **Azure Blob Storage** for long-term archival and replay if needed.
     - Inserts or updates the most recent record into **Azure SQL Database**, linking it to the `patient_id` in the patient registry.

3. **Sleep Data Ingestion (Wearable API → Storage)**  
   - Once per day, a scheduled process (either part of the Flask backend or a simple script) calls a **simulated wearable vendor API** to retrieve the previous night’s sleep data.  
   - The sleep readings (e.g., total sleep hours, sleep efficiency) are written as JSON/CSV files into **Azure Blob Storage**, organized by date and patient.

4. **Nightly Aggregation and Risk Scoring (Blob → Azure Function → SQL)**  
   - A **timer-triggered Azure Function** runs on a schedule (e.g., nightly).  
   - The Function:
     1. Lists new mood and sleep files in Blob Storage since the last run.
     2. Reads and parses these files.
     3. Aggregates data by `patient_id` and date, computing metrics such as:
        - 7-day average mood score.
        - Change in mood vs. baseline.
        - 7-day average sleep hours and presence of sleep deficit.
     4. Applies simple rules or a scoring formula to set a `risk_flag` (e.g., green / yellow / red) and `risk_reason` (e.g., “mood dropping”, “poor sleep”, “both”).  
   - The Function writes these aggregated results into **Azure SQL Database** in a summary table (e.g., `daily_patient_summary`).

5. **Clinician Dashboard (SQL → Flask → Browser)**  
   - When a clinician logs into the web dashboard (through a browser), the UI is served by the same **Flask app** running on App Service/Container Apps.  
   - Flask queries the **SQL database** to retrieve:
     - A list of patients with their current risk status and key metrics.
     - Time-series data for trend plots (e.g., last 7–30 days of mood and sleep).  
   - The dashboard presents:
     - A panel view (table) showing each patient’s risk flag (color coded).
     - Drill-down views to see individual patient trends and notes.

6. **Analytics and Model Experimentation (SQL ↔ Azure ML Notebook)**  
   - For more advanced analytics, an **Azure ML notebook** connects securely to **Azure SQL Database**.  
   - Data scientists or students can:
     - Pull historical mood and sleep data.
     - Explore correlations and patterns.
     - Train simple classification models or rule-based scores to predict worsening risk.  
   - The notebook can write back an additional column (e.g., `risk_score_ml`) into the SQL summary table.  
   - The Flask dashboard can then display both the rules-based risk flag from the Function and the model-based score from the notebook.

This flow demonstrates an integrated pipeline from **data ingestion → storage → processing → database → dashboard → analytics** using multiple Azure services together.

---

## 3. Security, Identity, and Governance Basics

Because this design touches mental health data, even in a synthetic classroom context, security and governance need to be considered from the beginning.

**Credential and secret management**

- Application secrets such as database connection strings or storage account keys are **not hard-coded** in the Flask or Function code.  
- Instead, they are stored using **Azure App Service application settings**, **Azure Function app settings**, or ideally **Azure Key Vault**.  
- The Flask app and Azure Function are configured to read required configuration values (e.g., DB connection, Blob container name) from environment variables at runtime.

**Access control and RBAC**

- Access to Azure resources (Blob Storage, SQL Database, ML workspace) is managed using **role-based access control (RBAC)**:
  - Developers and students receive restricted roles in a non-production subscription or resource group.
  - The Flask App Service and Azure Function use **managed identities** where possible, so they can securely access other services (e.g., Blob Storage, SQL) without storing passwords.  
- Clinician dashboard access would, in a real deployment, be protected by authentication (e.g., Azure Entra ID or another identity provider), ensuring that only authorized clinicians can view patient data.

**Avoiding real PHI in public / classroom environments**

- For this course project, **no real patient data** is used. All data is synthetic or de-identified and stored in a restricted laboratory environment.  
- In a real system, additional steps would be required:
  - Hosting all services in private networks with limited public endpoints.
  - Enforcing encryption in transit (HTTPS) and at rest (encrypted storage and databases).
  - Applying “minimum necessary” data principles so that only required fields are stored and displayed.

These practices help ensure that, even as a student project, the architecture reflects real-world expectations around privacy, security, and governance in mental health applications.

---

## 4. Cost and Operational Considerations

The architecture deliberately favors **managed** and **serverless** services to keep both complexity and cost low, especially in a student setting.

**Likely cost drivers**

- The main cost drivers would be:
  - The **compute resources** for the Flask app (App Service/Container Apps) if running 24/7.
  - The **managed database** (Azure SQL) if provisioned with higher performance tiers or large storage.  
- **Azure Blob Storage** is generally inexpensive even for relatively large amounts of JSON/CSV files, so storage is unlikely to be the primary cost concern.  
- **Azure ML** and advanced analytics workloads can become expensive if large compute clusters or GPUs are used, but for this project the notebook can be run on small, on-demand CPU instances.

**Using serverless and right-sized resources**

- The **Azure Function** is serverless and runs only on a schedule (e.g., nightly), which keeps processing costs low because it only consumes resources when it is actively running.  
- The Flask app can be hosted on a **small App Service plan** or a lightweight **Container Apps** configuration, enough for low traffic and demo usage.  
- The **Azure SQL Database** can use a basic or low DTU/vCore tier, sufficient for small datasets and a limited number of queries.

**Staying within a “student budget”**

To keep the solution affordable and aligned with free or low-cost tiers:

- Choose the **lowest reasonable tiers** for SQL and App Service and scale up only if needed.  
- Use a **single small database** rather than multiple databases.  
- Delete or stop unused resources after demonstrations or grading are complete.  
- Limit data volume (e.g., a small number of synthetic patients and a short time frame of data) to avoid unnecessary storage or compute costs.  

Overall, the design balances realism (using multiple integrated cloud services) with practicality and cost awareness so that it can fit within a classroom environment and, with adjustments, scale to a more robust deployment in the future.
