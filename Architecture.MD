# Architecture
 
Fully on-premises deployment. The dashed boundary in the diagram marks the customer environment — models, lakehouse, object storage and agent runtime all sit inside it.
 
![Architecture overview](../assets/architecture-overview.png)
 
## Step 1 — User
 
A process engineer or QA analyst asks a question in natural language about wafer defects, yield trends, trace data or defect scan images. No SQL, no BI tool, no ticket to the data team.
 
## Step 2 — Agent runtime
 
**watsonx Orchestrate** runs a ReAct agent backed by two on-prem models:
 
| Model | Role |
|---|---|
| IBM Granite | Natural-language understanding and tool selection, on-prem watsonx.ai |
| gpt-oss | Open-source model, self-hosted — no external inference calls |
 
The agent reasons about the question, decides which tools it needs, and emits an execution plan. That plan is logged and surfaced for human review before execution — the governance checkpoint.
 
## Step 3 — MCP tool registry
 
The agent picks tools based on query intent. Three are registered:
 
### SAL tool
Translates natural language into Presto SQL. It resolves business terms through the **Business Glossary** and discovers Iceberg schemas dynamically, so the generated query is grounded in real table structure rather than guessed column names.
 
Example of what it produces:
 
```sql
SELECT defect_code,
       COUNT(*) AS cnt,
       cnt / wafer_area AS density
FROM fact_defects              -- Iceberg table
WHERE lot = 'WF-2024-0391'
ORDER BY cnt DESC
LIMIT 5
```
 
### Chart rendering tool
Produces Pareto charts, yield trends, wafer maps, SPC charts and trace scatter plots as SVG/PNG returned inline in the chat.
 
### Image-from-object-storage tool
Fetches defect scan images from on-prem Ceph and renders them inline, so the engineer sees the number *and* the wafer it came from without switching tools.
 
## Data layer
 
### watsonx.data lakehouse
 
| Group | Tables |
|---|---|
| Wafer / lot | `fact_defects`, `dim_wafer_lots`, `dim_equipment` |
| Trace / defect | `fact_process_trace`, `yield_history` |
 
Queried through Presto over Apache Iceberg.
 
### Ceph object storage
 
Defect scan images stored as TIFF/PNG per lot and wafer, addressed as `ceph://defects/<lot>/scan_*.tiff`. A storage explorer allows browsing objects by lot or wafer.
 
## Governance
 
Enforced inside the on-prem boundary:
 
- Row-level access control
- Audit trails for every query and tool invocation
- IP-sensitive fab data protection
- Human-in-the-loop approval before any query executes
## Step 4 — Response
 
The chat window returns a combined response: the result table, the rendered chart, and the corresponding defect scan image — each labelled with the tool that produced it.
 
![Response detail](../assets/architecture-detail.png)
 
## Design notes
 
**Why MCP tools instead of one monolithic agent.** Each capability is independently testable and reusable across use cases. Adding a prediction tool later means registering one more tool, not rebuilding the agent.
 
**Why fully on-prem.** Fab process data and defect imagery are among the most IP-sensitive assets a semiconductor manufacturer holds. Self-hosted models remove the question of data leaving the environment entirely.
 
**Why human-in-the-loop.** Engineers need to trust the query before they trust the answer. Showing the reasoning trace and the generated SQL turns the agent from a black box into a reviewable assistant.
 
