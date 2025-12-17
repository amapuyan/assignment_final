# Reflection

## Confidence and Uncertainty in the Design

The parts of this architecture that I feel most confident about are the overall data flow and service integration. Mapping the mental health use case to cloud components such as Azure Blob Storage for raw data, Azure Functions for scheduled processing, and Azure SQL Database for structured summaries felt very intuitive and closely aligned with what we practiced throughout the course. I am especially comfortable with the idea of using a serverless function for nightly aggregation and risk flag generation, since it clearly separates ingestion from processing and keeps costs and operational complexity low. Designing the Flask-based API and dashboard as the access layer was also straightforward, as it mirrors prior work with APIs, Python, and managed cloud compute services.

The areas I feel least confident about are the **advanced security and analytics components**. While I understand the concepts of using managed identities, RBAC, and environment variables for credential management, I have less hands-on experience implementing enterprise-grade security controls in a production environment. Similarly, the AI and analytics portion of the design—such as training and validating a more sophisticated mental health risk model—was intentionally kept simple. Designing a clinically validated model, handling bias, and ensuring interpretability would require more domain expertise and significantly more time than was available for this project.

## Alternative Architecture Considered

One alternative architecture I considered was using a single virtual machine (VM) to host the Flask application, background processing scripts, and the database together. While this approach would simplify deployment by consolidating everything into one environment, it was not chosen because it introduces several drawbacks. A VM-based design increases operational overhead, requires manual scaling and patching, and does not align well with the serverless and managed service patterns emphasized in the course. It also makes cost optimization more difficult, as the VM would need to run continuously even when little or no data is being processed.

Another alternative was a **fully serverless and NoSQL-based design**, using only Blob Storage and a NoSQL database without a relational SQL layer. Although this approach could work for event-driven ingestion, it was not selected because relational databases are more suitable for structured reporting, aggregation, and dashboard queries. Since much of the course focused on SQL, managed databases, and analytics workflows, the Azure SQL–based design felt more appropriate and better aligned with the learning objectives.

## Future Improvements with More Time and Resources

If I had an additional 4–8 weeks and unlimited cloud credits, I would expand this project in several meaningful ways. First, I would implement a fully functional prototype, including authentication using Azure Entra ID and role-based access so that clinicians can securely log in and view only their assigned patients. Second, I would enhance the analytics component by training a more robust machine learning model using Azure ML, incorporating longer historical trends, variability metrics, and possibly natural language processing on patient journal entries.

Additionally, I would explore real-time or near-real-time alerts, such as sending notifications to clinicians when a patient crosses a high-risk threshold instead of relying solely on nightly batch processing. From an infrastructure perspective, I would also introduce monitoring and logging using Azure Monitor and Application Insights to track system health and usage patterns. Finally, I would work on improving governance and compliance by simulating HIPAA-aligned controls, such as private networking, audit logs, and stricter access policies, to make the system closer to a production-ready healthcare platform.

Overall, this project provided a valuable opportunity to integrate multiple cloud services into a cohesive healthcare solution while balancing realism, technical feasibility, and cost considerations.
