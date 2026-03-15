# Automation Specification: The WFI Weekly Friction Report

## 1. Context & Objective

The Welfare Footprint Institute (WFI) employs the **“Strategic No”** framework. We treat every expert rejection of an analytical output as a "knowledge-creation event." These rejections are captured manually by researchers in this repository using the `rejection-log` issue template.

**The Goal:** To prevent these insights from "evaporating" inside the repository. We require an automated pipeline that summarizes these rejections once a week and broadcasts the resulting "Encoded Constraints" to the team via Slack. This creates a feedback flywheel that raises the "Quality Bar" for the entire Institute.

---

## 2. System Architecture

The system should function as a scheduled job (e.g., GitHub Action or a Cron job) following this data flow:

1. **Extract:** Query the GitHub API for issues with the `rejection-log` label.
2. **Filter:** Select issues that were created or closed within the last 7 days.
3. **Process:** Send the issue content to an LLM (OpenAI/Claude) to synthesize the "Friction Report."
4. **Deliver:** Post the formatted report to the WFI Slack channel `#the-no-channel`.

---

## 3. Technical Requirements

### Data Source (GitHub API)

* **Target Repo:** `Center-for-Welfare-Metrics/philosophy-and-guidelines`
* **Filter Logic:**
* Label: `rejection-log`
* State: `all` (Capture both `open` debates and `closed` encodings).
* Since: `7 days ago`.



### Analytical Engine (LLM Prompt)

The system must pass the raw Markdown text of the filtered issues to an LLM with the following System Prompt:

> **System Prompt:**
> "You are the WFI Lead Quality Analyst. Your task is to review the attached 'Strategic No' logs from the past week and synthesize them into a 'Weekly Friction Report.'
> **Objectives:**
> 1. **Identify Patterns:** Detect if failures are clustering around specific taxa, metrics, or analytical tools.
> 2. **Distill Constraints:** Extract the 'Proposed New Rules' from each log. Ensure they are written as clear, imperative constraints for future AI prompts.
> 3. **Distinguish Status:** Differentiate between 'Open Debates' (Active Issues) and 'Encoded Constraints' (Closed Issues).
> 
> 
> **Tone:** Scientific, skeptical, and precise. Avoid 'corporate fluff.' Prioritize the technical 'No' over general progress."

### Output Delivery (Slack Integration)

* **Target:** Slack Webhook for `#the-no-channel`.
* **Format:** Block Kit or Markdown.
* **Schedule:** Every Friday at 16:00 UTC.

---

## 4. Report Structure (Template)

The LLM should output the report using this structure:

```markdown
# 📉 Weekly Friction Report [Date]

## ⚡ The Top-Line "Nos"
*A 1-2 sentence summary of the most critical analytical failures identified this week.*

## ✅ Encoded Constraints (Closed This Week)
*These are the new rules now integrated into our "Source of Truth."*
* **[Constraint Name]:** [The specific rule/instruction].
* **[Constraint Name]:** [The specific rule/instruction].

## 🧪 Open Debates (Active Rejections)
*Issues currently being articulated. Researchers are encouraged to jump into these threads.*
* **Issue #[Number]:** [Title] - *Current Status: [e.g., Awaiting taxonomic verification].*

## 🔍 Pattern Recognition
*Observation on whether the errors suggest a systemic flaw in a specific framework or tool.*

```

---

## 5. Developer Implementation Checklist

* [ ] **GitHub Access:** Configure a Personal Access Token (PAT) with `repo` scopes.
* [ ] **LLM Access:** Configure `OPENAI_API_KEY` or `ANTHROPIC_API_KEY`.
* [ ] **Slack Access:** Configure `SLACK_WEBHOOK_URL`.
* [ ] **Logic:** Implement a script (Python or Node.js) that handles the date filtering and API orchestration.
* [ ] **Environment:** Use GitHub Actions (`.github/workflows/friction-report.yml`) to run the script on a schedule.

---

## 6. Philosophy for the Developer

As Nate Jones argues: **"What you can verify, you can automate."** This script is the automation of our verification frontier. It ensures that once a senior researcher says "No," that rejection compounds into an institutional asset that never evaporates.
