# dbt-docgen-audit (V1)

_A Python-based tool for auditing and bootstrapping `schema.yml` docs in dbt projects._

This notebook helps data teams **audit, standardize, and generate documentation** for dbt models within a specific `marts/` folder, starting with `marts/executive/`.

---

## 🧠 What This Solves

Many dbt projects accumulate inconsistent, incomplete, or duplicated documentation across models. This tool:

- ✅ Audits model and column documentation completeness
- ✅ Reuses standardized `{% docs %}` definitions where possible
- ✅ Preserves existing inline documentation
- ✅ Flags conflicts and gaps for human review
- ✅ Outputs a clean, ready-to-paste `schema.yml` block

> ⚙️ **Tech stack:** Python, dbt, YAML parsing, SQL metadata (from `information_schema.columns`)

---

## 📦 What This Outputs

- ✅ A fully-formed `schema.yml` block
- 🆕 Suggested `{% docs %}` blocks for reuse
- ⚠️ A conflict report for mismatched column descriptions
- 📊 A diagnostic summary of coverage (e.g., documented vs. undocumented columns)

### 🧾 Example Output (schema.yml block)

```yaml
columns:
  - name: user_id
    description: '{{ doc("user_id") }}'
  - name: session_id
    description: 'Unique identifier for the web session'
  - name: signup_date
    description: '{{ doc("signup_date") }}'
```

---

## 🔁 How the Tool Works

1. **Discovery**
   - Scans the `marts/executive/` folder for `.sql` models
   - Prints a warehouse query based on the table names discovered
   - Assumes manual metadata export as a CSV from your data warehouse
   - Loads that CSV to power the documentation workflow

2. **Assembly**
   - Reuses `{% docs %}` where available
   - Preserves inline descriptions if `{% docs %}` is not found
   - Suggests new `{% docs %}` candidates
   - Flags conflicting descriptions for review

3. **Output**
   - Clean `schema.yml` block ready for use
   - Suggested reusable documentation blocks
   - Summary of documentation quality

---

## 🛠️ Metadata Input (Manual Export – V1 Constraint)

This tool currently uses a **manually exported CSV file** from your warehouse’s `information_schema.columns` metadata. This allows the notebook to run without requiring DB credentials or fragile connection setup.

> ⚠️ V1 intentionally avoids setting up a live Python → warehouse connection for simplicity and portability.

### ✅ Workflow

1. Run the auto-generated SQL query (printed by the notebook) in your data warehouse or Databricks environment:
    ```sql
    SELECT table_schema, table_name, column_name, ordinal_position
    FROM information_schema.columns
    WHERE table_name IN ('table_1', 'table_2', ...)
    ```
2. Export the result as a CSV (UTF-8 encoded).
3. Save the file as:
    ```
    column_metadata_export.csv
    ```
4. Place the file in the same directory as this notebook (or update the file path).

The notebook will then:
- Load this metadata
- Map columns to table names
- Begin the documentation audit and generation process

---

## 🔒 Current Scope – V1

> 📌 This version is scoped to the `marts/executive/` subfolder of the shared `optauto_assets` schema.  
Other AEs may maintain different folders under the same schema. The folder name can be adjusted via a single line in the model discovery step.

This V1 was intentionally scoped to:
- Focus on a real, high-value documentation use case
- Avoid premature abstraction across all team folders
- Prove the value of column doc reuse before generalizing

Future versions may parameterize the target folder or enable reuse across multiple AE-owned marts.


---

## 🔭 Future Adaptations

- 📁 Parameterize mart folder selection
- 🧰 Refactor into a CLI or modular script
- 🧪 Add validation + unit testing
- 🧠 Improve `{% docs %}` parsing to support name mismatches
- 📈 Generate doc coverage dashboards or summaries
- 🪪 Integrate with ownership metadata for doc routing

---

**Built by [Jason Garbacz](https://www.linkedin.com/in/jason-garbacz)**  
Currently exploring roles focused on data tooling, analytics engineering, or GPT-backed automation systems.

---

_This project is shared as a demonstration of personal contribution to internal tooling.  
It is not licensed for commercial use or redistribution._

