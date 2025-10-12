# Implementation Guide: Building the Power Platform Governance & Security Sentinel

**Version:** 1.0  
**Last Updated:** October 9, 2025  
**Estimated Time:** 8-12 hours for complete implementation

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Phase 1: Environment Setup](#phase-1-environment-setup)
3. [Phase 2: Building the Dataverse Data Model](#phase-2-building-the-dataverse-data-model)
4. [Phase 3: Creating Power Automate Flows](#phase-3-creating-power-automate-flows)
5. [Phase 4: Building the Model-Driven App](#phase-4-building-the-model-driven-app)
6. [Phase 5: Creating the Power BI Dashboard](#phase-5-creating-the-power-bi-dashboard)
7. [Phase 6: Testing and Validation](#phase-6-testing-and-validation)
8. [Phase 7: Deployment and Go-Live](#phase-7-deployment-and-go-live)
9. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before beginning the implementation, ensure you have the following:

### Required Roles and Permissions

| Role | Purpose | Required For |
| :--- | :--- | :--- |
| **Power Platform Administrator** | Access to Power Platform for Admins connectors and tenant-wide data | All phases |
| **System Administrator** (Dataverse) | Create tables, configure security roles | Phases 2, 4 |
| **Environment Maker** | Create apps and flows | Phases 3, 4 |

### Required Licenses

| License | Purpose | Quantity |
| :--- | :--- | :--- |
| **Power Apps Premium** or **Per App** | Model-Driven App access | 1+ (for admins) |
| **Power Automate Premium** | Power Platform for Admins connectors | 1 (service account) |
| **Power BI Pro** or **Premium Per User** | Dashboard creation and viewing | 1+ (for admins) |

### Technical Requirements

- **Dedicated Power Platform Environment** with Dataverse enabled (Production or Sandbox)
- **Service Account** for running flows (recommended to prevent disruption if users leave)
- **Dedicated Mailbox** for notifications (optional but recommended)
- **Power BI Desktop** installed on your local machine
- **Modern Web Browser** (Edge, Chrome, or Firefox)

### Data Loss Prevention (DLP) Considerations

Ensure that the following connectors are in the **same data group** in your DLP policies:

- Power Platform for Admins
- Microsoft Dataverse
- Office 365 Outlook (for notifications)

If these connectors are in different groups, the flows will be blocked by DLP policies.

---

## Phase 1: Environment Setup

### Step 1.1: Create a Dedicated Environment

While you can install the solution in an existing environment, creating a dedicated environment is recommended for better isolation and management.

1. Navigate to the [Power Platform Admin Center](https://admin.powerplatform.microsoft.com/)
2. Select **Environments** from the left navigation
3. Click **+ New** on the command bar
4. Configure the environment:
   - **Name:** `Power Platform Governance`
   - **Type:** Production (or Sandbox for testing)
   - **Region:** Select your preferred region
   - **Add a Dataverse data store:** Yes
   - **Security Group:** None (or select a specific group if required)
5. Click **Next** and then **Save**
6. Wait for the environment to be provisioned (this may take several minutes)

### Step 1.2: Configure Security Roles

Once the environment is created, configure security roles to control access.

1. In the Power Platform Admin Center, select your new environment
2. Click **Settings** in the command bar
3. Expand **Users + permissions** and select **Security roles**
4. Verify that the following roles exist:
   - **System Administrator** (for you and other admins)
   - **System Customizer** (for makers who will extend the solution)
   - **Basic User** (for read-only access to the Model-Driven App)

### Step 1.3: Create a Service Account

Creating a dedicated service account ensures that flows continue to run even if individual users leave the organization.

1. In your Microsoft 365 Admin Center, create a new user:
   - **Name:** `Power Platform Governance Service`
   - **Username:** `pp-governance@yourdomain.com`
   - **Password:** Use a strong, randomly generated password and store it securely
2. Assign the following licenses to the service account:
   - Power Automate Premium
   - Power Apps Premium (if using Per User licensing)
3. In the Power Platform Admin Center, add this user to your environment with the **System Administrator** role
4. Grant the service account the **Power Platform Administrator** role in Microsoft 365 (required for Power Platform for Admins connectors)

---

## Phase 2: Building the Dataverse Data Model

In this phase, you will create the custom tables that form the foundation of the governance data repository.

### Step 2.1: Navigate to the Dataverse Table Designer

1. Navigate to [Power Apps](https://make.powerapps.com/)
2. Select your **Power Platform Governance** environment from the environment picker
3. In the left navigation, select **Tables**
4. Click **+ New table** and select **Set advanced properties**

### Step 2.2: Create the "Governance - Environment" Table

This table stores information about each Power Platform environment.

1. In the **New table** panel, configure:
   - **Display name:** `Governance - Environment`
   - **Plural name:** `Governance - Environments`
   - **Primary column name:** `Environment Name`
   - **Enable attachments:** No
2. Click **Save**
3. Add the following columns by clicking **+ New column**:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **Environment ID** | Single line of text (max 100) | Yes | Unique identifier from Power Platform |
| **Environment Type** | Choice | No | Options: Production, Sandbox, Trial, Developer |
| **Region** | Single line of text (max 50) | No | Geographic region |
| **Created Date** | Date and Time | No | When the environment was created |
| **Is Default** | Yes/No | No | Whether this is the default environment |

4. Click **Save Table**

### Step 2.3: Create the "Governance - Owner" Table

This table stores information about app and flow owners.

1. Click **+ New table** and select **Set advanced properties**
2. Configure:
   - **Display name:** `Governance - Owner`
   - **Plural name:** `Governance - Owners`
   - **Primary column name:** `Display Name`
3. Click **Save**
4. Add the following columns:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **User ID** | Single line of text (max 100) | Yes | Azure AD User ID (GUID) |
| **Email** | Email | No | User's email address |
| **Account Status** | Choice | No | Options: Active, Disabled, Deleted |
| **Department** | Single line of text (max 100) | No | User's department |
| **Last Sync Date** | Date and Time | No | Last time user data was synced |

5. Click **Save Table**

### Step 2.4: Create the "Governance - Power App" Table

This table stores information about each Power App.

1. Click **+ New table** and select **Set advanced properties**
2. Configure:
   - **Display name:** `Governance - Power App`
   - **Plural name:** `Governance - Power Apps`
   - **Primary column name:** `App Name`
3. Click **Save**
4. Add the following columns:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **App ID** | Single line of text (max 100) | Yes | Unique app identifier (GUID) |
| **Display Name** | Single line of text (max 200) | No | User-friendly app name |
| **App Type** | Choice | No | Options: Canvas, Model-Driven |
| **Created Date** | Date and Time | No | When the app was created |
| **Modified Date** | Date and Time | No | Last modification date |
| **Is Shared** | Yes/No | No | Whether the app is shared with others |
| **Shared Count** | Whole Number | No | Number of users/groups shared with |
| **Connector Count** | Whole Number | No | Number of connectors used |
| **Last Launched** | Date and Time | No | Last time the app was launched |
| **Launch Count** | Whole Number | No | Total number of launches |

5. Add the following **Lookup** columns to create relationships:

| Column Display Name | Data Type | Related Table | Description |
| :--- | :--- | :--- | :--- |
| **Environment** | Lookup | Governance - Environment | The environment containing this app |
| **Owner** | Lookup | Governance - Owner | The owner of this app |

6. Click **Save Table**

### Step 2.5: Create the "Governance - Power Automate Flow" Table

This table stores information about each Power Automate Flow.

1. Click **+ New table** and select **Set advanced properties**
2. Configure:
   - **Display name:** `Governance - Power Automate Flow`
   - **Plural name:** `Governance - Power Automate Flows`
   - **Primary column name:** `Flow Name`
3. Click **Save**
4. Add the following columns:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **Flow ID** | Single line of text (max 100) | Yes | Unique flow identifier (GUID) |
| **Display Name** | Single line of text (max 200) | No | User-friendly flow name |
| **State** | Choice | No | Options: Started, Stopped, Suspended |
| **Created Date** | Date and Time | No | When the flow was created |
| **Modified Date** | Date and Time | No | Last modification date |
| **Trigger Type** | Single line of text (max 100) | No | Type of trigger (e.g., Recurrence, Manual) |
| **Action Count** | Whole Number | No | Number of actions in the flow |
| **Connector Count** | Whole Number | No | Number of connectors used |
| **Last Run** | Date and Time | No | Last time the flow ran |
| **Run Count** | Whole Number | No | Total number of runs |

5. Add the following **Lookup** columns:

| Column Display Name | Data Type | Related Table | Description |
| :--- | :--- | :--- | :--- |
| **Environment** | Lookup | Governance - Environment | The environment containing this flow |
| **Owner** | Lookup | Governance - Owner | The owner of this flow |

6. Click **Save Table**

### Step 2.6: Create the "Governance - Connector Usage" Table

This junction table maps connectors to the apps and flows that use them.

1. Click **+ New table** and select **Set advanced properties**
2. Configure:
   - **Display name:** `Governance - Connector Usage`
   - **Plural name:** `Governance - Connector Usages`
   - **Primary column name:** `Connector Name`
3. Click **Save**
4. Add the following columns:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **Connector ID** | Single line of text (max 100) | No | Unique connector identifier |
| **Connector Tier** | Choice | No | Options: Standard, Premium, Custom |
| **Risk Level** | Choice | No | Options: Low, Medium, High, Critical |
| **Asset Type** | Choice | Yes | Options: Power App, Power Automate Flow |
| **Asset ID** | Single line of text (max 100) | Yes | ID of the app or flow using this connector |

5. Add the following **Lookup** columns:

| Column Display Name | Data Type | Related Table | Description |
| :--- | :--- | :--- | :--- |
| **Power App** | Lookup | Governance - Power App | The app using this connector (if applicable) |
| **Power Automate Flow** | Lookup | Governance - Power Automate Flow | The flow using this connector (if applicable) |

6. Click **Save Table**

### Step 2.7: Create the "Governance - Risk Flag" Table

This table stores flagged items that require administrative review.

1. Click **+ New table** and select **Set advanced properties**
2. Configure:
   - **Display name:** `Governance - Risk Flag`
   - **Plural name:** `Governance - Risk Flags`
   - **Primary column name:** `Risk Description`
3. Click **Save**
4. Add the following columns:

| Column Display Name | Data Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **Asset Type** | Choice | Yes | Options: Power App, Power Automate Flow |
| **Asset ID** | Single line of text (max 100) | Yes | ID of the flagged asset |
| **Risk Type** | Choice | Yes | Options: Data Leakage, Orphaned, Non-Compliant, Security |
| **Severity** | Choice | Yes | Options: Low, Medium, High, Critical |
| **Flagged Date** | Date and Time | Yes | When the risk was identified |
| **Reviewed** | Yes/No | No | Whether an admin has reviewed this flag |
| **Reviewer** | Single line of text (max 100) | No | Name of the admin who reviewed |
| **Review Date** | Date and Time | No | When the review occurred |
| **Review Notes** | Multiple lines of text | No | Admin notes about the review |
| **Remediation Status** | Choice | No | Options: Open, In Progress, Resolved, Accepted Risk |

5. Add the following **Lookup** columns:

| Column Display Name | Data Type | Related Table | Description |
| :--- | :--- | :--- | :--- |
| **Power App** | Lookup | Governance - Power App | The flagged app (if applicable) |
| **Power Automate Flow** | Lookup | Governance - Power Automate Flow | The flagged flow (if applicable) |

6. Click **Save Table**

### Step 2.8: Verify the Data Model

After creating all tables, verify that the relationships are correctly established:

1. In the **Tables** list, select **Governance - Power App**
2. Click on the **Relationships** tab
3. Verify that you see relationships to:
   - Governance - Environment (Many-to-One)
   - Governance - Owner (Many-to-One)
   - Governance - Connector Usage (One-to-Many)
   - Governance - Risk Flag (One-to-Many)

Repeat this verification for the **Governance - Power Automate Flow** table.

---

## Phase 3: Creating Power Automate Flows

In this phase, you will create the automated flows that harvest data from the Power Platform tenant.

### Step 3.1: Create Connection References

Before creating flows, establish the necessary connections.

1. Navigate to [Power Automate](https://make.powerautomate.com/)
2. Ensure you are in the **Power Platform Governance** environment
3. In the left navigation, select **Data** > **Connections**
4. Click **+ New connection**
5. Create connections for:
   - **Power Platform for Admins** (sign in with the service account)
   - **Microsoft Dataverse** (sign in with the service account)
   - **Office 365 Outlook** (optional, for notifications)

### Step 3.2: Create the "Scan - Harvest All Environments" Flow

This flow retrieves all environments and populates the Governance - Environment table.

1. In Power Automate, click **+ Create** > **Scheduled cloud flow**
2. Configure:
   - **Flow name:** `Scan - Harvest All Environments`
   - **Starting:** Tomorrow at 2:00 AM
   - **Repeat every:** 1 Day
3. Click **Create**
4. Add the following actions:

**Action 1: Get Environments as Admin**
- **Connector:** Power Platform for Admins
- **Action:** `Get Environments as Admin`
- **Parameters:** Leave all blank to get all environments

**Action 2: Apply to each (Loop through environments)**
- **Connector:** Control
- **Action:** `Apply to each`
- **Select an output from previous steps:** `value` (from the Get Environments action)

Inside the loop, add:

**Action 3: Upsert Environment to Dataverse**
- **Connector:** Microsoft Dataverse
- **Action:** `Add a new row` or `Update a row` (use Upsert if available)
- **Table name:** `Governance - Environments`
- **Environment Name:** `items('Apply_to_each')?['properties']?['displayName']`
- **Environment ID:** `items('Apply_to_each')?['name']`
- **Environment Type:** Use a condition to map `items('Apply_to_each')?['properties']?['environmentSku']` to your choice values
- **Region:** `items('Apply_to_each')?['location']`
- **Created Date:** `items('Apply_to_each')?['properties']?['createdTime']`
- **Is Default:** `if(equals(items('Apply_to_each')?['properties']?['isDefault'], true), true, false)`

5. Click **Save**
6. Click **Test** > **Manually** > **Run flow** to verify it works

### Step 3.3: Create the "Scan - Harvest All Power Apps" Flow

This flow retrieves all Power Apps and populates the Governance - Power App table.

1. Create a new **Scheduled cloud flow**:
   - **Flow name:** `Scan - Harvest All Power Apps`
   - **Schedule:** Daily at 3:00 AM
2. Add the following actions:

**Action 1: List all environments from Dataverse**
- **Connector:** Microsoft Dataverse
- **Action:** `List rows`
- **Table name:** `Governance - Environments`

**Action 2: Apply to each environment**
- **Action:** `Apply to each`
- **Select an output:** `value` (from List rows)

Inside the environment loop:

**Action 3: Get Apps as Admin**
- **Connector:** Power Platform for Admins
- **Action:** `Get Apps as Admin`
- **Environment Name:** `items('Apply_to_each')?['cr6f3_environmentid']` (use the Environment ID column)

**Action 4: Apply to each app**
- **Action:** `Apply to each`
- **Select an output:** `value` (from Get Apps as Admin)

Inside the app loop:

**Action 5: Get App Details as Admin**
- **Connector:** Power Platform for Admins
- **Action:** `Get App as Admin`
- **Environment Name:** `items('Apply_to_each')?['cr6f3_environmentid']`
- **App Name:** `items('Apply_to_each_2')?['name']`

**Action 6: Parse JSON (to extract app details)**
- **Connector:** Data Operation
- **Action:** `Parse JSON`
- **Content:** `body('Get_App_as_Admin')`
- **Schema:** Use the "Use sample payload to generate schema" feature and paste a sample app JSON response

**Action 7: Upsert App to Dataverse**
- **Connector:** Microsoft Dataverse
- **Action:** `Add a new row` (with duplicate detection or manual upsert logic)
- **Table name:** `Governance - Power Apps`
- **App Name:** `body('Parse_JSON')?['properties']?['displayName']`
- **App ID:** `body('Parse_JSON')?['name']`
- **Display Name:** `body('Parse_JSON')?['properties']?['displayName']`
- **App Type:** Determine from `body('Parse_JSON')?['properties']?['appType']`
- **Created Date:** `body('Parse_JSON')?['properties']?['createdTime']`
- **Modified Date:** `body('Parse_JSON')?['properties']?['lastModifiedTime']`
- **Environment:** Link to the Dataverse environment record
- **Owner:** You'll need to create/lookup the owner record first (see next step)

**Action 8: Handle Owner (before Action 7)**
- First, check if the owner exists in the Governance - Owner table
- If not, create a new owner record
- Then link the app to the owner

3. Click **Save** and **Test**

### Step 3.4: Create the "Scan - Harvest All Power Automate Flows" Flow

Follow a similar pattern to Step 3.3, but use:
- `Get Flows as Admin` instead of `Get Apps as Admin`
- `Get Flow as Admin` for details
- Upsert to the **Governance - Power Automate Flows** table

### Step 3.5: Create the "Analyze - Detect Data Leakage Risks" Flow

This flow analyzes connector usage and flags high-risk combinations.

1. Create a new **Scheduled cloud flow**:
   - **Flow name:** `Analyze - Detect Data Leakage Risks`
   - **Schedule:** Daily at 5:00 AM (after harvest flows complete)
2. Add the following logic:

**Action 1: List all connector usages**
- **Connector:** Microsoft Dataverse
- **Action:** `List rows`
- **Table name:** `Governance - Connector Usages`
- **Filter rows:** `cr6f3_risklevel eq 'High' or cr6f3_risklevel eq 'Critical'`

**Action 2: Apply to each high-risk connector usage**
- **Action:** `Apply to each`

Inside the loop:

**Action 3: Check if already flagged**
- **Connector:** Microsoft Dataverse
- **Action:** `List rows`
- **Table name:** `Governance - Risk Flags`
- **Filter rows:** `cr6f3_assetid eq 'ASSET_ID' and cr6f3_risktype eq 'Data Leakage'`

**Action 4: Condition - If not already flagged**
- **Condition:** `length(outputs('Check_if_already_flagged')?['body/value']) eq 0`

**Action 5: Create Risk Flag**
- **Connector:** Microsoft Dataverse
- **Action:** `Add a new row`
- **Table name:** `Governance - Risk Flags`
- Populate all required fields

3. Click **Save** and **Test**

### Step 3.6: Create the "Analyze - Identify Orphaned Assets" Flow

This flow identifies apps and flows where the owner account is disabled or deleted.

1. Create a new **Scheduled cloud flow**:
   - **Flow name:** `Analyze - Identify Orphaned Assets`
   - **Schedule:** Daily at 5:30 AM
2. Add logic to:
   - List all owners with Account Status = "Disabled" or "Deleted"
   - Find all apps and flows owned by these users
   - Create risk flags for each orphaned asset

---

## Phase 4: Building the Model-Driven App

In this phase, you will create the administrative interface for managing governance data.

### Step 4.1: Create a New Model-Driven App

1. Navigate to [Power Apps](https://make.powerapps.com/)
2. Select your **Power Platform Governance** environment
3. Click **+ Create** > **Model-driven app**
4. In the modern app designer:
   - **Name:** `Power Platform Governance Admin`
   - **Description:** `Administrative interface for Power Platform governance and security`
5. Click **Create**

### Step 4.2: Add Tables to the App

1. In the app designer, click **+ Add page**
2. Select **Dataverse table**
3. Add the following tables:
   - Governance - Power Apps
   - Governance - Power Automate Flows
   - Governance - Risk Flags
   - Governance - Environments
   - Governance - Owners
   - Governance - Connector Usages
4. For each table, select the views you want to include (e.g., Active, Inactive, All)

### Step 4.3: Configure the Site Map (Navigation)

1. In the app designer, click on **Navigation**
2. Create the following navigation structure:

**Group 1: Inventory**
- Power Apps
- Power Automate Flows
- Environments

**Group 2: Governance**
- Risk Flags
- Connector Usages

**Group 3: Administration**
- Owners

3. Click **Save** and then **Publish**

### Step 4.4: Test the Model-Driven App

1. Click **Play** to launch the app
2. Verify that you can:
   - View all tables
   - Navigate between different areas
   - Open individual records
   - Edit and save records

---

## Phase 5: Creating the Power BI Dashboard

In this phase, you will create the interactive dashboard for visualizing governance data.

### Step 5.1: Install Power BI Desktop

If you haven't already, download and install [Power BI Desktop](https://powerbi.microsoft.com/desktop/).

### Step 5.2: Connect to Dataverse

1. Open Power BI Desktop
2. Click **Get Data** > **More**
3. Search for and select **Dataverse**
4. Click **Connect**
5. Enter your **Environment URL** (e.g., `https://yourorg.crm.dynamics.com/`)
6. Click **OK**
7. Sign in with your credentials
8. In the Navigator, select all the Governance tables:
   - Governance - Power Apps
   - Governance - Power Automate Flows
   - Governance - Risk Flags
   - Governance - Environments
   - Governance - Owners
   - Governance - Connector Usages
9. Click **Load**

### Step 5.3: Create the Executive Summary Page

1. Rename the first page to **Executive Summary**
2. Add the following visuals:

**Card Visual 1: Total Apps**
- **Fields:** Count of App ID from Governance - Power Apps
- **Title:** Total Power Apps

**Card Visual 2: Total Flows**
- **Fields:** Count of Flow ID from Governance - Power Automate Flows
- **Title:** Total Power Automate Flows

**Card Visual 3: High-Risk Flags**
- **Fields:** Count of Risk Flag ID from Governance - Risk Flags
- **Filter:** Severity = High or Critical
- **Title:** High-Risk Items

**Line Chart: Apps Created Over Time**
- **X-axis:** Created Date (by Month)
- **Y-axis:** Count of App ID
- **Title:** App Creation Trend

**Donut Chart: Apps by Environment Type**
- **Legend:** Environment Type
- **Values:** Count of App ID
- **Title:** Apps by Environment Type

### Step 5.4: Create the Risk Dashboard Page

1. Add a new page named **Risk Dashboard**
2. Add the following visuals:

**Table Visual: All Risk Flags**
- **Columns:** Risk Description, Asset Type, Severity, Flagged Date, Reviewed
- **Title:** All Risk Flags

**Bar Chart: Risks by Type**
- **Axis:** Risk Type
- **Values:** Count of Risk Flag ID
- **Title:** Risk Distribution

**Slicer: Filter by Severity**
- **Field:** Severity

### Step 5.5: Create the Connector Analysis Page

1. Add a new page named **Connector Analysis**
2. Add visuals to show:
   - Most used connectors
   - Premium vs. Standard connector usage
   - High-risk connectors

### Step 5.6: Save and Publish

1. Click **File** > **Save As**
2. Save the file as `PowerPlatformGovernanceDashboard.pbix`
3. Click **Publish** > Select your workspace
4. Share the dashboard with other administrators

---

## Phase 6: Testing and Validation

### Step 6.1: Validate Data Collection

1. Check that the harvest flows have run successfully
2. Verify that data appears in the Dataverse tables
3. Confirm that relationships between tables are correct

### Step 6.2: Validate Risk Detection

1. Manually create a test scenario (e.g., an app using a high-risk connector)
2. Run the risk detection flows
3. Verify that risk flags are created correctly

### Step 6.3: Validate User Experience

1. Have other administrators test the Model-Driven App
2. Gather feedback on usability and functionality
3. Test the Power BI dashboard with real data

---

## Phase 7: Deployment and Go-Live

### Step 7.1: Export the Solution

1. In Power Apps, navigate to **Solutions**
2. Click **+ New solution**
3. Name it `Power Platform Governance Sentinel`
4. Add all components (tables, flows, apps)
5. Export as a **Managed** solution

### Step 7.2: Document the Deployment

1. Create deployment notes
2. Document any customizations made
3. Create a runbook for ongoing maintenance

### Step 7.3: Train Administrators

1. Conduct training sessions for administrators
2. Provide documentation and user guides
3. Establish support processes

---

## Troubleshooting

### Issue: Flows are failing with "Forbidden" errors

**Solution:** Verify that the service account has the Power Platform Administrator role and that DLP policies allow the necessary connectors.

### Issue: No data appearing in Dataverse

**Solution:** Check the flow run history for errors. Verify that the connections are authenticated correctly.

### Issue: Power BI dashboard shows no data

**Solution:** Ensure that the Dataverse connection is using DirectQuery mode and that you have the necessary permissions.

### Issue: Duplicate records in Dataverse

**Solution:** Implement proper upsert logic in the flows using the unique ID fields as keys.

---

## Next Steps

After successful implementation:

1. Schedule regular reviews of risk flags
2. Customize the solution to meet your organization's specific needs
3. Contribute improvements back to the open-source project
4. Share your experience with the community

For support and contributions, visit the [GitHub repository](https://github.com/rkneela0912/powerplatform-governance-sentinel).

