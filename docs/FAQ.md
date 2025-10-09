# Frequently Asked Questions

## General Questions

**Q: What is the Power Platform Governance & Security Sentinel?**

A: It is an open-source solution that provides automated governance and security monitoring for Microsoft Power Platform environments. It scans your tenant, collects metadata about apps, flows, and connectors, and presents this information through interactive dashboards and administrative tools.

**Q: Why do I need this solution?**

A: As Power Platform adoption grows, it becomes increasingly difficult for IT and security teams to maintain visibility and control over the apps and automations being created. This solution addresses that challenge by providing a centralized, automated governance system that helps you identify risks, ensure compliance, and prevent "shadow IT" from becoming a security liability.

**Q: Is this an official Microsoft product?**

A: No, this is a community-driven, open-source project. It is not affiliated with or endorsed by Microsoft. However, it is built entirely on the Microsoft Power Platform using official connectors and best practices.

## Installation and Configuration

**Q: What licenses do I need to use this solution?**

A: You will need Power Apps Premium (or Per App), Power Automate Premium, and Power BI Pro (or Premium Per User) licenses. The flows use the Power Platform for Admins connectors, which require premium licenses.

**Q: Can I install this in a trial environment?**

A: Yes, you can install it in a trial environment for testing purposes. However, for production use, we recommend installing it in a dedicated, production-grade environment.

**Q: How long does the initial scan take?**

A: The duration of the initial scan depends on the size of your tenant. For a tenant with hundreds of apps and flows, the scan may take 30-60 minutes. Subsequent scans are typically faster as they only update changed items.

**Q: Will this solution work if I have Data Loss Prevention (DLP) policies in place?**

A: Yes, but you need to ensure that the Power Platform for Admins connectors are in the same data group as the Dataverse connector in your DLP policies. If they are in different groups, the flows will be blocked.

## Usage and Features

**Q: How often does the solution scan my tenant?**

A: By default, the scanning flows are scheduled to run daily. You can adjust the schedule by editing the recurrence trigger in each flow to meet your organization's needs.

**Q: Can I customize the risk detection rules?**

A: Yes, the solution is designed to be extensible. You can create additional flows to implement custom risk detection logic based on your organization's specific policies and requirements.

**Q: Does this solution automatically remediate issues?**

A: The current version (v1.0) focuses on detection and reporting. It does not automatically remediate issues. However, future versions will include automated remediation workflows, and you can extend the solution yourself to add this functionality.

**Q: Can I export the data for use in other systems?**

A: Yes, all data is stored in Dataverse, which can be accessed via the Dataverse Web API. You can export the data to other systems using Power Automate flows, Azure Data Factory, or custom code.

## Troubleshooting

**Q: The flows are failing with a "Forbidden" error. What should I do?**

A: This typically indicates a permissions issue. Ensure that the service account running the flows has the Power Platform Administrator role and that the connections are properly configured. Also, check your DLP policies to ensure the connectors are not blocked.

**Q: The Power BI dashboard is not showing any data. What's wrong?**

A: First, verify that the scanning flows have run successfully and that data has been written to Dataverse. Then, check that the Power BI report is connected to the correct Dataverse environment and that you have the necessary permissions to access the data.

**Q: I'm seeing duplicate records in the Dataverse tables. How do I fix this?**

A: This can happen if the flows run multiple times simultaneously or if there is an issue with the upsert logic. Check the flow run history to identify any errors. You may need to manually clean up duplicate records and then adjust the flow logic to prevent future duplicates.

## Contributing and Support

**Q: How can I contribute to this project?**

A: We welcome contributions! Please read the [CONTRIBUTING.md](../CONTRIBUTING.md) file for guidelines on how to submit issues, feature requests, and pull requests.

**Q: Where can I get help if I encounter an issue?**

A: You can open an issue on the GitHub repository. The community and project maintainers will do their best to assist you. Please provide as much detail as possible, including error messages, screenshots, and steps to reproduce the issue.

**Q: Is there a community forum or chat for this project?**

A: We are currently evaluating options for a community forum or chat. In the meantime, please use the GitHub Issues and Discussions features for community interaction.

