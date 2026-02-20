Identify your assigned tasks from the worker assignment table in 00-plan.md and implement them.

Only reference files within the Report Directory from Piece Context.

**What to do:**
1. Read 00-plan.md and obtain `feature` and `tasks_path` from "## Target Feature"
2. Check the worker assignment table in "Current Batch" of 00-plan.md
3. Identify the tasks assigned to your worker name (worker-1 or worker-2)
4. If no tasks are assigned, report "no assigned tasks"
5. If there are assigned tasks:
   a. Read `{tasks_path}` to check the details of assigned tasks (read-only)
   b. Implement each task
   c. Add unit tests for newly created classes and functions
   d. Update relevant tests when existing code is modified
   e. Test file placement: follow the project's conventions
   f. Test execution is mandatory. After implementation is complete, run tests and verify results
6. If an unrecoverable error occurs during implementation, report "implementation failed"

**Important:**
- Due to `session: refresh`, always obtain the feature from 00-plan.md
- Only implement your assigned tasks
- Do not touch tasks assigned to other workers
- Do not modify files other than those touched by your assigned tasks
- Do not update tasks.md checkboxes (they are updated collectively in merge-batch)
- If information conflicts, prioritize the reports in the Report Directory and actual file contents

**Required output (include these headings)**
## Assigned Tasks
- {List of task IDs}
## Work Results
- {Summary of work performed}
## Changes Made
- {Summary of changes}
## Completed Tasks
- {List of task IDs whose implementation is complete}
## Test Results
- {Commands executed and results}
