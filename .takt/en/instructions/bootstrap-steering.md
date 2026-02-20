Analyze the codebase and generate initial steering files in `.kiro/steering/`.

**What to do:**
1. Load templates from `.takt/knowledge/steering-template-files/`
2. Analyze the codebase using JIT (fetch when needed):
   - Investigate the source file structure
   - Read project definition files such as README, package.json, Cargo.toml, etc.
   - Search for code patterns
3. Extract patterns (not lists):
   - **product.md**: Product purpose, value, core features
   - **tech.md**: Frameworks, technical decisions, conventions
   - **structure.md**: Code organization, naming conventions, import patterns
4. Generate steering files following the templates
5. Save to the `.kiro/steering/` directory (create if it does not exist)

**JIT Strategy:** Fetch information only when needed. Do not read all files in advance.

**Focus:** Document patterns that guide decision-making. Do not create catalogs of files or modules.

**Artifact destination:**
- Directory: `.kiro/steering/` (create if it does not exist)
- Files: `product.md`, `tech.md`, `structure.md`
