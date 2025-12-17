# Cloud Integration Mini-Capstone – Mental Health Monitoring System

## Project Overview

This project presents a **cloud-based healthcare solution** designed as a mini-capstone for the Cloud Integration course. The solution focuses on **mental health monitoring** using daily mobile check-ins and wearable sleep data to give clinicians better visibility into patient trends between visits.

The goal of the project is to demonstrate how multiple cloud services can be **integrated end-to-end**—including storage, compute, databases, and analytics—to solve a realistic healthcare problem. The project emphasizes **architecture design**, service integration, and trade-off analysis rather than building a full production system.

---

## Healthcare Use Case

Patients with depression or anxiety often experience significant changes in mood and sleep between clinical visits. Without continuous monitoring, clinicians may miss early warning signs of deterioration.

This system enables:
- Daily mood and anxiety check-ins via a mobile app
- Integration of sleep data from wearable devices
- Automated aggregation and risk flagging in the cloud
- A clinician-facing dashboard showing trends and alerts

All data used in this project is **synthetic or de-identified** and intended solely for educational purposes.

---

## Cloud Platform

- **Primary platform:** Microsoft Azure  
- **Key services used:**  
  - Azure Blob Storage  
  - Azure App Service / Azure Container Apps  
  - Azure Functions  
  - Azure SQL Database  
  - Azure ML / Notebook (optional analytics)

---

## Repository Structure

```text
assignment_final/
  README.md                  # High-level project overview (this file)
  use_case.md                # Detailed healthcare use case description
  architecture_plan.md       # Architecture & implementation plan
  architecture_diagram.png   # High-level system diagram (or embedded in markdown)
  reflection.md              # Design reflection and future considerations

