# Solution Package

This folder will contain the Power Platform solution package (`.zip` file) that can be imported into your environment.

## Building the Solution

The solution is currently under development. Once the initial version is complete, the managed and unmanaged solution packages will be available in the [Releases](https://github.com/yourusername/powerplatform-governance-sentinel/releases) section of the GitHub repository.

## Contents

The solution package includes:

*   **Dataverse Tables:** Custom tables for storing governance data.
*   **Power Automate Flows:** Automated flows for scanning the tenant and analyzing data.
*   **Model-Driven App:** Administrative interface for managing governance data.
*   **Connection References:** References to the required connectors.
*   **Environment Variables:** Configuration settings for the solution.

## Manual Installation

If you prefer to build the solution manually, you can follow these steps:

1.  Create a new solution in your Power Platform environment.
2.  Create the Dataverse tables as defined in the [ARCHITECTURE.md](../docs/ARCHITECTURE.md) document.
3.  Import the Power Automate flows from the `flows` folder (to be added).
4.  Create the Model-Driven App and configure it to use the custom tables.
5.  Configure the connection references and environment variables.

Detailed step-by-step instructions for manual installation will be provided in a future update.

