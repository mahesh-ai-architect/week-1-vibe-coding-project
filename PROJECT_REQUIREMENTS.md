# Incident Log Analyser — Project Requirements

## Why This Project Was Started

I work as an Architect in the observability space. I embarked on this project to learn Agentic AI concepts and expand my skills in observing and analysing AI systems.

In line with my professional experience, I created the **Incident Log Analyser** using vibe coding. It is a Streamlit-based application that accepts incident CSV files and produces operational insights based on severity, affected service, Mean Time to Resolution (MTTR), acknowledgement performance, SLA outcomes, and executive reporting requirements.

The project provides a practical foundation for exploring how AI-enabled systems can support incident analysis, operational decision-making, and observability workflows.

## 1. Overview

### What is it?

Incident Log Analyser is a local Streamlit dashboard for analysing incident records. It provides:

- Incident volume and severity analysis.
- MTTR and acknowledgement-performance metrics.
- Service, team, and date filtering.
- Interactive charts with incident drill-down.
- SLA policy configuration and breach analysis.
- Incident searching, sorting, and CSV export.
- Executive reports with period comparisons and service-level metrics.

### Who is it for?

The application is intended for:

- Observability and Site Reliability Engineering teams.
- Incident managers and service owners.
- Operations and support teams.
- Engineering leaders and executives who need concise incident trends.
- Architects and developers learning AI-assisted application development.

### Problem solved

Incident information is often stored as raw CSV data that is difficult to interpret quickly. The application converts that data into consistent metrics, visual trends, SLA outcomes, searchable records, and executive-ready reports.

It helps users answer questions such as:

- Which services and teams generate the most incidents?
- How many high-severity incidents occurred?
- How quickly are incidents acknowledged and resolved?
- Which incidents breached their configured SLA targets?
- Are incident outcomes improving or worsening compared with the previous period?

### Access

The application is accessed through a web browser after starting the local Streamlit server. The default address is:

```text
http://localhost:8501
```

No login, API key, or external AI service is required for the current version.

### Data

The application loads the bundled `incident_log_sample.csv` file automatically. Users can upload another UTF-8 CSV file through the sidebar. Uploaded files must contain the required incident fields described in [Section 3](#3-data-requirements).

Invalid opened timestamps are excluded with a data-quality note. Invalid, missing, negative, or infinite timing values are treated as unknown.

### Technology

- Python 3.10 or newer.
- Streamlit for the user interface and dashboard runtime.
- Pandas and Python-based analysis components for data processing.
- Plotly-based interactive visualisations.
- CSV for incident data exchange.
- JSON for exporting and importing SLA policies.
- Markdown and HTML for executive report output.

### Hosting

The application is currently designed for local hosting and execution. It runs as a Streamlit process on the user's Windows or other supported development machine. A future version could be deployed to an internal server or cloud-hosted Streamlit environment, subject to security, authentication, data-retention, and operational requirements.

## 2. Business Goals

The project shall:

1. Provide a simple way to turn raw incident logs into useful operational insight.
2. Reduce the manual effort required to prepare incident and executive summaries.
3. Improve visibility into incident severity, service concentration, team impact, acknowledgement performance, and MTTR.
4. Make SLA performance measurable using configurable severity-specific targets.
5. Allow users to investigate individual incidents behind aggregate charts and reports.
6. Support repeatable analysis by allowing incident CSV files and SLA policies to be exchanged.
7. Provide a practical learning project for vibe coding, Streamlit development, and observability of AI-enabled systems.
8. Preserve deterministic, evidence-based reporting without inferring customer impact or root cause from incomplete data.

## 3. Data Requirements

### 3.1 About `incident_log_sample.csv`

`incident_log_sample.csv` is the bundled demonstration dataset. It is loaded automatically when the application starts unless the user uploads a replacement CSV file.

The CSV must be UTF-8 encoded and contain a header row. The required columns are:

```text
incident_id,opened_at,service,team,severity,region,ack_minutes,resolve_minutes,status,summary
```

Additional columns may be present but are ignored by the core analysis.

The sample data contains incident records covering multiple services, teams, severities, regions, statuses, acknowledgement times, and resolution times. It is intended for demonstration and validation rather than as a production incident system of record.

### 3.2 Field rules

| Field | Requirement and interpretation |
| --- | --- |
| `incident_id` | Identifies the incident. Duplicate IDs are retained and flagged; counts are based on CSV rows. |
| `opened_at` | Required timestamp used for date filtering, reporting periods, and weekly analysis. Timestamps without an offset are interpreted as UTC; explicit offsets are converted to UTC. Invalid timestamps are excluded. |
| `service` | Affected service used for filtering, grouping, charts, and service reporting. |
| `team` | Owning or responsible team used for filtering and analysis. |
| `severity` | Incident severity, such as SEV1 through SEV4. Used for severity charts and SLA policy matching. |
| `region` | Incident region or operational location. Retained as incident data and searchable in the explorer. |
| `ack_minutes` | Time from incident opening to acknowledgement. Missing, nonnumeric, negative, or infinite values are unknown. |
| `resolve_minutes` | Time used for MTTR and resolution SLA evaluation. Missing, nonnumeric, negative, or infinite values are unknown. |
| `status` | Current incident status. Only `resolved` and `closed` count as resolved; other values count as unresolved. |
| `summary` | Human-readable incident description used in search and reports. |

Additional data rules:

- All timestamps are displayed and filtered in UTC.
- MTTR is the mean of valid `resolve_minutes` values, regardless of incident status.
- The acknowledgement benchmark counts known `ack_minutes <= 15` against all incidents in scope.
- Empty scopes display `N/A` for applicable metrics.
- Unknown acknowledgement durations are excluded from the acknowledgement histogram.
- Duplicate rows are not silently removed.
- Reports describe the current status of incidents opened in the selected reporting period.

## 4. Layout and Navigation

The application shall use a Streamlit dashboard layout with:

### Sidebar

- CSV upload control.
- Inclusive opened-date range filter.
- Severity, service, and team multiselect filters.
- Reset filters control.
- SLA policy settings section.
- Severity-specific acknowledgement and resolution target inputs.
- Apply SLA policy control.
- SLA policy download and JSON import controls.

### Main content

The main area shall provide tabs or equivalent navigation for:

1. **Overview** — KPI cards, incident charts, SLA outcomes, breach totals, and noisiest services.
2. **Incident explorer** — Searchable, sortable, filterable incident table with CSV download.
3. **Executive report** — Reporting period selection, service scope, report preview, and downloads.
4. **Selected chart incidents** — Detail records resulting from chart selections and drill-down.

The interface shall provide clear feedback for empty data scopes, invalid records, uploaded files, active filters, and selected chart criteria.

## 5. Functional Requirements

### Data loading and filtering

- The system shall load the bundled sample CSV on startup.
- The system shall allow a user to upload a replacement CSV.
- The system shall validate required columns and provide a useful data-quality message when data is invalid.
- The system shall filter incidents by date, severity, service, and team.
- Empty multiselect filters shall match no incidents.
- Reset filters shall restore the full loaded dataset.

### Metrics and visualisations

- The system shall display total incidents, MTTR, SEV1 count, and acknowledgement compliance within 15 minutes.
- The system shall display incident trends by severity, service, SLA outcome, weekly MTTR, and acknowledgement duration.
- The system shall identify the five noisiest services.
- Charts shall support selecting one or more points where applicable.
- Chart selections shall narrow the selected-incident detail view while leaving dashboard KPIs and charts within the sidebar scope.
- Users shall be able to clear chart selections.

### Incident explorer

- The system shall search across incident columns.
- The system shall support table sorting.
- The system shall allow matching records to be downloaded as CSV.
- The system shall optionally show SLA breaches only.
- Confirmed breach records shall be visually distinguished from unknown outcomes.

### SLA policy

- Users shall be able to configure acknowledgement and resolution targets by severity.
- Users shall be able to enable or disable a severity policy.
- Applying a policy shall update dashboard results, incident tables, and executive reports consistently.
- A duration greater than its configured target shall be a breach; a duration equal to its target shall meet the SLA.
- The system shall classify incidents as breached, met, unknown, or not configured.
- An incident shall count once in the overall breach total even if both acknowledgement and resolution targets are breached.
- Users shall be able to export and import SLA policies as JSON.

### Executive reporting

- Users shall be able to select Last 24 hours, Last week, or Last month.
- Users shall be able to select one service or all services in the active service scope.
- The report shall compare the selected period with an equivalent preceding period.
- The report shall include incident volume, MTTR, SEV1 count, acknowledgement compliance, unresolved counts, and SLA breaches.
- The report shall include repeated incidents, notable incidents, and evidence-based recommended actions.
- The report shall provide HTML, Markdown, service-metrics CSV, and SLA-breach CSV downloads.
- The standalone HTML report shall be suitable for browser printing or saving as PDF.
- Reports shall identify the reference time used, including whether current UTC time or the latest incident in the file was selected.

## 6. Reporting

Reporting shall be designed for both operational investigation and executive communication.

### Operational reporting

Operational users shall receive:

- Current incident counts and severity distribution.
- MTTR and acknowledgement performance.
- Service and team concentration.
- SLA status by severity and service.
- Searchable incident-level evidence.
- Downloadable filtered incident data.

### Executive reporting

Executive reports shall summarise the selected reporting period and compare it with an equivalent previous period. The report shall clearly identify:

- Overall incident volume and change.
- MTTR and change from the previous period.
- High-severity incident counts.
- Acknowledgement compliance.
- Unresolved incidents.
- Services with the greatest incident or SLA-breach concentration.
- Repeated or notable incidents.
- Specific breached incidents, actual durations, targets, and minutes overdue.
- Evidence-based recommended actions.

### Reporting principles

- Reports must be based on the loaded data and active filters.
- The selected reporting period replaces the sidebar date range; severity, service, and team filters continue to apply.
- Table search shall not alter report scope.
- The active SLA policy shall be applied consistently to current and comparison periods.
- Reports shall distinguish confirmed breaches, unknown outcomes, and unconfigured policies.
- The application shall not infer customer impact, root cause, or business consequences that are not supported by the source data.

## Reference

The implementation and run instructions are maintained in [README.md](./README.md). The primary application entry point is [app.py](./app.py).
