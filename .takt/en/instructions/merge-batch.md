Consolidate the results from parallel workers and batch-update the checkboxes in tasks.md.

**What to do:**
1. Read 00-plan.md and obtain `feature` and `tasks_path` from "## Target Feature"
   - If `feature` or `tasks_path` is missing, terminate without making updates
2. Read the worker reports (worker-1-results.md, worker-2-results.md)
   - If a report does not exist or lacks a completed tasks section, skip that worker as "no completed tasks" and continue processing
3. Extract task IDs from each worker's "Completed Tasks" section
4. Update the checkboxes of the corresponding tasks in `{tasks_path}`
   - `- [ ]` -> `- [x]`
5. Skip workers that did not report any completed tasks

**Important:**
- Use `tasks_path` from 00-plan.md as the sole source of truth for the update target
- Only update task IDs reported by workers
- Do not modify other parts of tasks.md
- If a report is not found, skip it (the worker may have had "no assigned tasks")

**Required output (include these headings)**
## Consolidation Results
- Target feature: {feature}
- Update target: {tasks_path}
- Updated task IDs: {list of IDs or none}
- Skipped task IDs: {list of IDs or none}
