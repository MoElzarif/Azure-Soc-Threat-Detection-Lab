# Azure SOC & Threat Detection Lab

**Microsoft Sentinel · Azure Activity Logs · Log Analytics · KQL**

## Overview

I built this lab to practice how a SOC detects and investigates activity in Microsoft Azure. I collected Azure management logs, wrote a detection rule for resource group deletion, tested it with a disposable resource group, and reviewed the incident in Microsoft Sentinel.

**Goal:** Follow one event from an Azure action to a Sentinel alert and investigation.

## 1. Set a budget

**Screenshot:** `screenshots/00-cost-gate.png`

I created a CAD $10 monthly budget for my SOC resource group, `rg-soc-lab`, with spending alerts.

**Why:** Cloud labs can generate charges. I wanted a budget in place before setting up monitoring.

## 2. Create a Log Analytics workspace

**Screenshot:** `screenshots/01-workspace.png`

I created `law-soc-lab-k27x` in Canada Central.

**Why:** I needed a place to collect and query Azure logs.

## 3. Enable Microsoft Sentinel

**Screenshot:** `screenshots/02-sentinel.png`

I connected Microsoft Sentinel to the Log Analytics workspace.

**Why:** Sentinel lets me turn log searches into detections and incidents.

## 4. Collect Azure Activity logs

**Screenshot:** `screenshots/03-azure-activity-data.png`

I configured Azure Activity ingestion and checked that management operations appeared in the `AzureActivity` table.

**Why:** A detection rule is only useful if the underlying logs are arriving.

## 5. Check baseline activity

**Screenshot:** `screenshots/04-baseline-activity.png`

I used a disposable resource group, `rg-soc-detection-test`, and checked its activity. The logs showed a successful tag update. The original resource group creation event was not captured in the available results.

**Why:** I wanted to confirm the test group's activity was visible before running the detection test.

## 6. Create the detection rule

**Screenshot:** `screenshots/05-analytics-rule.png`

I created a scheduled Sentinel rule named **SOC-LAB-001 - Resource Group Deleted**. It looks for successful resource group deletion operations in Azure Activity logs.

**Why:** I wanted Sentinel to flag this action automatically instead of searching for it by hand.

**Detection query:** [`kql/resource-group-deleted.kql`](kql/resource-group-deleted.kql)

## 7. Test with a real deletion

**Screenshot:** `screenshots/06-delete-event.png`

I confirmed the test resource group contained no resources, deleted it, and checked Azure Activity for the successful deletion event.

**Why:** Testing against a real event helped me confirm the logs and detection were connected.

## 8. Investigate the incident

**Screenshots:** `screenshots/07-incident.png` and `screenshots/08-investigation.png`

I reviewed the Sentinel incident and checked the event details, including the operation, status, time, and affected resource group. The activity was part of my authorized lab test.

**Why:** A SOC analyst needs to understand what happened, not just acknowledge an alert.

## 9. Hunt through logs and build a workbook

**Screenshot:** `screenshots/10-workbook.png`

I ran KQL searches and saved a workbook named **SOC Lab - Azure Activity Monitoring** with a chart of the top Azure Activity operations.

**Why:** The workbook makes it easier to see patterns without writing the same query every time.

## Optional: Incident notification playbook

I tried setting up `la-soc-notify-incident` with an Office 365 Outlook email action. The connection returned **401 Unauthorized**, so I did not complete or claim a successful notification run.

The optional `09-playbook-run.png` evidence is therefore not included.

## What I learned

- How Azure Activity logs reach Log Analytics.
- How to use KQL to search for management operations.
- How scheduled Sentinel analytics rules detect specific events.
- How to test a rule and investigate the resulting incident.
- How to use workbooks to summarize activity.

## Evidence and privacy

The screenshot paths above are **placeholders until the actual images are uploaded**. See [`screenshots/README.md`](screenshots/README.md) for the evidence checklist.

Before publishing any screenshot, I will remove personal email addresses, subscription IDs, caller identities, and other sensitive details.

## Lab status

**Built and tested:** log ingestion, deletion detection, incident investigation, and workbook.

**Not yet documented as complete:** final Azure cleanup and subscription-level diagnostic setting / policy cleanup. The optional email notification playbook was not completed.

This is a personal learning project, not a production SOC deployment.
