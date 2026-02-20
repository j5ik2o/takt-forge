Read tasks.md, analyze incomplete tasks, and create a batch execution plan.

**Note:** If a Previous Response exists, this is a re-plan after the previous batch completed.
Check the check status in tasks.md and determine the next batch.

**What to do:**
1. Identify the target feature name
   - Initial run (no 00-plan.md): Extract the feature name from the User Task
   - Re-plan (00-plan.md exists): Prioritize "## Target Feature" from the existing 00-plan.md
   - If unable to identify, determine "requirements unclear, insufficient information"
2. Establish `tasks_path = .kiro/specs/{feature}/tasks.md`
3. Read `{tasks_path}`
   - If the file does not exist, determine "tasks.md does not exist or is empty"
   - Same if the file is empty or contains no tasks (`- [ ]` / `- [x]`)
4. Identify incomplete tasks (`- [ ]`)
5. If all tasks are complete (`- [x]`), determine "all tasks complete"
6. If there are incomplete tasks:
   a. Estimate the implementation scope of each task (number of files changed, complexity)
   b. Investigate the code to identify the impact scope
   c. Determine the batch:
      - Group tasks that touch the same files into the same batch
      - Limit each batch to a range where related source code fits in context
      - Tasks with (P) markers that have no file conflicts are parallel batch candidates
   d. Determine the batch type:
      - Sequential batch: Task groups with dependencies, or task groups without (P) markers
      - Parallel batch: Two or more task groups with (P) markers that are mutually independent
7. Decide the implementation approach
   - Cross-check against knowledge and policy constraints

**Batch type determination:**
- If there are 2+ incomplete (P) tasks that are mutually file-conflict-free and each task is Medium scope or larger -> Parallel batch
- Small-scope tasks are not parallelized; group them in sequential batches (the startup overhead of merge-batch negates the benefits of parallelization)
- Otherwise -> Sequential batch
- When in doubt, choose sequential batch (err on the safe side)

**Batch size determination criteria:**
- Small (1-2 files changed): Up to 5-8 tasks per batch
- Medium (3-5 files changed): Up to 2-3 tasks per batch
- Large (6+ files changed): 1 task per batch

**Parallel batch worker assignment:**
- Distribute evenly to worker-1 and worker-2
- Verify that the files each worker touches do not overlap
- tasks.md itself is not a worker modification target (updated collectively in merge-batch)

**Required output:**

```markdown
# Batch Execution Plan

## Target Feature
- feature: {feature}
- tasks_path: `.kiro/specs/{feature}/tasks.md`

## Progress
- Completed: {completed task count}/{total task count}
- Not started: {not started task count}

## Batch Type
Sequential / Parallel

## Current Batch
| Task ID | Task Summary | Worker Assignment |
|---------|-------------|-------------------|
| X.X | ... | - / worker-1 / worker-2 |

## Implementation Approach
{Implementation strategy for tasks in the batch}

## Implementation Guidelines
- {Guidelines the Coder should follow}
```
