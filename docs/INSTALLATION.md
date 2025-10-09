# Installation Guide

This guide will walk you through the process of installing and configuring the Power Platform Governance & Security Sentinel in your environment.

## Prerequisites

Before you begin, ensure you have the following:

1.  **Power Platform Environment:** A dedicated environment (preferably a production environment) with Dataverse enabled.
2.  **Administrative Permissions:** You must have the following roles:
    *   **Power Platform Administrator** or **Global Administrator** in your Microsoft 365 tenant.
    *   **System Administrator** role in the target Power Platform environment.
3.  **Licenses:** The following licenses are required:
    *   **Power Apps Premium** or **Power Apps Per App** for the Model-Driven App.
    *   **Power Automate Premium** for the flows (due to the use of the Power Platform for Admins connectors).
    *   **Power BI Pro** or **Power BI Premium Per User** for the dashboard.
4.  **Power Platform for Admins Connectors:** Ensure that the Power Platform for Admins connectors are not blocked by your tenant's Data Loss Prevention (DLP) policies.

## Installation Steps

### Step 1: Download the Solution

Download the latest release of the solution package from the [Releases](https://github.com/yourusername/powerplatform-governance-sentinel/releases) page. The solution will be provided as a `.zip` file.

### Step 2: Import the Solution

1.  Navigate to [Power Apps](https://make.powerapps.com/).
2.  Select the environment where you want to install the solution.
3.  In the left navigation pane, select **Solutions**.
4.  Click **Import** on the command bar.
5.  Click **Browse** and select the downloaded solution `.zip` file.
6.  Click **Next**.
7.  Review the solution information and click **Next**.
8.  On the **Connections** page, you will need to create or select existing connections for the following connectors:
    *   **Power Platform for Admins**
    *   **Dataverse**
9.  Click **Import** and wait for the import process to complete. This may take several minutes.

### Step 3: Configure Connection References

After the solution is imported, you need to configure the connection references:

1.  In the **Solutions** area, open the **Power Platform Governance & Security Sentinel** solution.
2.  Navigate to **Connection References**.
3.  For each connection reference, click on it and select the appropriate connection from the dropdown. If a connection does not exist, create a new one.
4.  Save your changes.

### Step 4: Run the Initial Setup Flow

The solution includes a setup flow that creates the necessary data structures and performs initial configuration.

1.  In the solution, navigate to **Cloud flows**.
2.  Locate the flow named **Setup - Initialize Governance Data**.
3.  Click on the flow to open it.
4.  Click **Run** to execute the flow.
5.  Monitor the flow run to ensure it completes successfully.

### Step 5: Trigger the Initial Tenant Scan

Once the setup is complete, you can trigger the main flows to perform the first full scan of your tenant.

1.  In the solution, navigate to **Cloud flows**.
2.  Locate the flow named **Scan - Harvest All Power Apps**.
3.  Click **Run** to execute the flow.
4.  Repeat this process for the following flows:
    *   **Scan - Harvest All Power Automate Flows**
    *   **Scan - Harvest All Connectors**

These flows are scheduled to run automatically on a regular basis (e.g., daily), but you can trigger them manually for the initial data population.

### Step 6: Connect the Power BI Dashboard

1.  Download the Power BI template file (`.pbit`) from the `powerbi` folder in the repository.
2.  Open the file in **Power BI Desktop**.
3.  When prompted, enter the **Dataverse environment URL** (e.g., `https://yourorg.crm.dynamics.com/`).
4.  Sign in with your credentials when prompted.
5.  The report will load and connect to your Dataverse instance.
6.  Customize the report as needed and publish it to your Power BI workspace.

## Post-Installation Configuration

### Scheduling

By default, the scanning flows are scheduled to run daily. You can adjust the schedule by editing the recurrence trigger in each flow.

### DLP Considerations

Ensure that the Power Platform for Admins connectors are in the same data group as the Dataverse connector in your DLP policies. Otherwise, the flows will fail.

### Permissions

Grant appropriate permissions to the Model-Driven App and the Power BI dashboard to the administrators and security team members who will be using the solution.

## Troubleshooting

If you encounter issues during installation, please refer to the [FAQ](FAQ.md) or open an issue on the GitHub repository.

