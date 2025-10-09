# Power BI Dashboard

This folder contains the Power BI template file (`.pbit`) for the Governance & Security Sentinel dashboard.

## Getting Started

1.  Download the `GovernanceSentinel.pbit` file from this folder.
2.  Open the file in Power BI Desktop.
3.  When prompted, enter your Dataverse environment URL (e.g., `https://yourorg.crm.dynamics.com/`).
4.  Sign in with your credentials.
5.  The dashboard will load with data from your environment.

## Dashboard Pages

The dashboard includes the following pages:

| Page Name | Description |
| :--- | :--- |
| **Executive Summary** | High-level KPIs and trends for your Power Platform tenant. |
| **App Inventory** | Detailed list of all Power Apps with filters and drill-through capabilities. |
| **Flow Inventory** | Detailed list of all Power Automate Flows with filters and drill-through capabilities. |
| **Connector Analysis** | Breakdown of connector usage by type, tier, and risk level. |
| **Risk Dashboard** | Displays all flagged items that require administrative review. |
| **Orphaned Assets** | Lists apps and flows where the owner account is disabled or no longer exists. |

## Customization

You can customize the dashboard to meet your organization's specific needs:

*   Add new pages or visuals.
*   Modify the color scheme and branding.
*   Create custom measures and calculated columns.
*   Add bookmarks and drill-through actions.

## Publishing

Once you have customized the dashboard, you can publish it to your Power BI workspace:

1.  In Power BI Desktop, click **File > Publish**.
2.  Select the target workspace.
3.  Click **Select**.
4.  Once published, you can share the dashboard with other users in your organization.

## Requirements

*   Power BI Desktop (latest version recommended)
*   Power BI Pro or Power BI Premium Per User license
*   Access to the Dataverse environment where the solution is installed

