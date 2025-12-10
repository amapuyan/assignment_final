# Use Case: Mental Health Monitoring via Mobile Check-In App
## Problem Statement: 
Many patients living with depression, anxiety, or other mood disorders only interact with their therapist or psychiatrist every few weeks or months. This project proposes a cloud-based mental health monitoring system that combines daily mobile check-ins and sleep data from wearables to provide clinicians with a continuous view of how their patients are doing between visits. The goal is not to replace therapy, but to give therapists better visibility into trends, so they can identify high-risk patients earlier, prioritize outreach, and adjust treatment plans when needed.
## Users:
- **Patients:**
Adults or adolescents being treated for depression, anxiety, or related mood disorders. They use a mobile app to complete a short daily check-in and optionally connect their wearable device.
- **Clinicians (therapists / psychiatrists)**
They review summary dashboards showing each patient's recent mood and sleep trends, with flags indicating patients who may be deteriorating or at higher risk.
- **Clinic Care Coordinators / Case Managers**
They monitor a panel-wide view and can help schedule earlier appointments or outreach for flagged patients.

## Data Sources:
The system uses several types of data. In this project, all data would be synthetic or de-identified, but the structure reflects realistic mental health monitoring. 
1. **Daily Mood Check-In (from Mobile App) - JSON or form posts**
- Collected via: mobile app or web front-end calling a Flask API.
- Example fields:
    - 'patient_id'
    - 'timestamp'
    - 'mood_score' (ex. 1-10 scale)
    - 'anxiety_score' (ex. 1-10 scale)
    - 'energy_level' (ex. low/medium/high or 1-5)
    - 'stress_level'
    - An optional text note ('journal_text')
2. **Sleep Data from Wearable Device API**
   - Synced from: wearable vendor API (e.g., simulated Fitbit / Apple Watch data) once per day.
   - Example fields:
     - `patient_id`
     - `date`
     - `total_sleep_hours`
     - `sleep_efficiency` (percentage of time in bed actually asleep)
     - Optional: `time_to_fall_asleep`, `wake_episodes`
3. **Patient Registry / Panel (Stored in Database)**
   - Used for: linking mood/sleep records to the right clinician.
   - Example fields:
     - `patient_id`
     - `display_name` (or pseudonym)
     - `diagnosis_category` (e.g., depression, anxiety)
     - `clinician_id`
     - `consent_flag` (whether they opted into remote monitoring)
## Workflow:
...
