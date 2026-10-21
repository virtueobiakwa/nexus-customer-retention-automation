# Customer Retention Automation & Follow-up Monitoring

**Project 5 | Days 17–20 of the Nexus 21-Day Data & Automation Portfolio**

## Overview

This project demonstrates an automated customer-retention operations workflow using **n8n, MySQL and Gmail**. It combines a batch-processing workflow for retention interventions with a separate monitoring workflow that classifies outstanding interventions by age and sends an internal summary email.

## Business problem

Customer-retention teams need a consistent way to identify interventions requiring attention, assign follow-up responsibility and monitor overdue work. Manual processes can leave records unassigned or allow urgent cases to go unnoticed.

## Objectives

- Retrieve customer-retention intervention records from MySQL.
- Prepare and update eligible intervention records for follow-up.
- Track outstanding interventions and classify their urgency using elapsed time.
- Generate a consolidated internal monitoring summary.
- Verify database state and test email delivery without contacting customers.

## Tools

| Tool | Purpose |
| --- | --- |
| MySQL | Intervention records, statuses and SQL queries |
| n8n | Batch-processing and scheduled monitoring workflows |
| Gmail | Internal monitoring report delivery |

## Workflows

### 1. Automated Customer Retention Management

A manually triggered n8n workflow retrieves eligible retention interventions, prepares high-risk retention actions and updates records in MySQL. Its query selects interventions with `Status = 'Pending'`.

The project previously processed **373 interventions**, setting their status to **In Progress** and assigning them to the **Customer Retention Team**. A later read-only MySQL check confirmed **373 In Progress** interventions and **no Pending** records. Consequently, rerunning the intake query at that point returned no records; this was consistent with the completed batch, not proof of a workflow failure.

**Verification note:** The exported workflow's nodes were renamed for clarity. Its empty-result path was checked afterward, but downstream update nodes were **not re-executed** after renaming because doing so could repeat database updates.

### 2. Customer Retention Follow-up Monitoring

A separate six-node workflow was configured for a daily **8:00 AM** monitoring schedule:

1. Schedule Trigger
2. Execute SQL query
3. Classify Retention Follow-up
4. Check Follow-up Required
5. Prepare Daily Retention Summary
6. Send a message (Gmail)

The monitoring logic categorises interventions by elapsed time:

| Age of open intervention | Category |
| --- | --- |
| Less than 24 hours | Normal |
| 24 to less than 48 hours | Follow-up Required |
| 48 hours or more | Escalation Required |

These thresholds are **portfolio demonstration rules**, not verified contractual service levels.

## Verified monitoring test results

| Measure | Result |
| --- | ---: |
| Interventions retrieved | **373** |
| Escalation Required | **373** |
| Follow-up Required | **0** |
| Normal | **0** |
| High-priority interventions | **373** |
| Oldest open intervention | **106 hours** |
| Consolidated summary records | **1** |
| Gmail test | **Sent; inbox receipt confirmed** |

The summary counts reflect one verified test execution, not continuous live monitoring or real customer-retention outcomes. The daily monitoring workflow was tested successfully; its ongoing publication/activation was **not confirmed**.

## Repository contents

- `Nexus - Automated Customer Retention Management.json` — batch retention management workflow export.
- `Nexus - Customer Retention Follow-up Monitoring.json` — monitoring and email summary workflow export.
- `Nexus_Retention_Automation_Workflow.png` — screenshot of the monitoring workflow canvas.
- `README.md` — project documentation.

Actual filenames may vary slightly from the examples above.

## How to explore

1. Review the screenshot to understand the monitoring workflow structure.
2. Import the JSON files separately into n8n to inspect the nodes and expressions.
3. Review SQL statements and configure your own MySQL connection, Gmail credentials and sample records before attempting tests.
4. Use **synthetic data** in a test database. Avoid rerunning database updates against the original processed records.
5. Keep scheduled email delivery disabled until recipients, credentials and notification frequency are explicitly reviewed.

## Business value

This project demonstrates how retention intervention records can be moved into a managed follow-up queue, monitored by elapsed time and summarised for operational review. It provides a practical example of connecting analytical outputs to workflow automation.

## Limitations and publication safety

- These are portfolio demonstration records; **373 processed interventions do not represent 373 customers retained**.
- The monitored age values were observed at one point in time.
- No unattended scheduled run has been independently verified.
- Before public upload, inspect n8n JSON exports and screenshots for email addresses, database details, credentials, webhook URLs and any personal information. Remove or redact sensitive material.

## Portfolio context

The fifth of five connected projects: Customer Data Quality → Business Operations Intelligence → Service Request & SLA Management → Customer Churn Analytics → **Customer Retention Automation**.
