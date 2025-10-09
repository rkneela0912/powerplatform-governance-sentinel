# Project Roadmap

This document outlines the planned features and enhancements for the Power Platform Governance & Security Sentinel project.

## Current Version: v1.0 (Initial Release)

The initial release includes the core functionality:

*   Automated scanning of Power Apps, Power Automate Flows, and Connectors.
*   Dataverse backend for storing governance data.
*   Power BI dashboard with key governance insights.
*   Model-Driven Admin App for managing flagged items.
*   Detection of data leakage risks and orphaned assets.

## Planned Features

### Version 1.1 (Q2 2025)

*   **Enhanced Connector Risk Analysis:** Implement a more sophisticated risk scoring algorithm that considers not just the connector type, but also the data sources it accesses and the permissions it requires.
*   **Owner Notification System:** Add flows that automatically notify app and flow owners when their assets are flagged for review or when they are identified as orphaned.
*   **Compliance Reporting:** Generate pre-built compliance reports (e.g., GDPR, HIPAA) based on the governance data.
*   **Integration with Azure AD:** Enrich owner data with information from Azure AD, such as department, manager, and account status.

### Version 1.2 (Q3 2025)

*   **Canvas App for User Attestation:** Develop a Canvas App that allows app and flow owners to periodically attest that their assets are still in use and compliant with company policy.
*   **Automated Archival Workflow:** Implement a workflow that automatically archives or deletes apps and flows that have been inactive for a specified period.
*   **Power Virtual Agents Integration:** Create a chatbot that allows administrators to query the governance data using natural language (e.g., "Show me all apps using the Twitter connector").
*   **Custom Connector Support:** Extend the solution to analyze and report on custom connectors in addition to standard connectors.

### Version 2.0 (Q4 2025)

*   **AI-Powered Anomaly Detection:** Use Azure Machine Learning or AI Builder to detect anomalous patterns in app and flow usage that could indicate security threats or policy violations.
*   **Integration with Microsoft Sentinel:** Send high-risk alerts to Microsoft Sentinel for centralized security monitoring.
*   **Multi-Tenant Support:** Extend the solution to support organizations with multiple Power Platform tenants.
*   **Advanced Data Lineage Tracking:** Implement data lineage tracking to show how data flows through apps, flows, and connectors, providing a complete picture of data movement within the tenant.

## Community Contributions

We welcome community contributions to help accelerate the roadmap. If you have a feature request or would like to contribute code, please open an issue or submit a pull request.

## Long-Term Vision

The long-term vision for this project is to create a comprehensive governance and security platform for the Power Platform that:

*   Provides real-time visibility into all Power Platform assets.
*   Automatically detects and remediates security and compliance risks.
*   Empowers administrators with actionable insights and automated workflows.
*   Serves as a reference implementation for Power Platform governance best practices.

