# Galaxy Workflow: Adding Shovill Estimated Sequencing Depth to CheckM Report

## Overview

This guide adds the Shovill estimated sequencing depth as a new column in the CheckM QC table in your Galaxy Australia report.

## Workflow Diagram

```
Shovill log collection
    ↓
Step 1: grep (extract depth line)
    ↓
Step 2: Collapse Collection
    ↓
Step 3: sed (strip depth label from values)
    ↓
Step 4: sed (insert header line)
    ↓
Step 5: Join two Datasets (with CheckM output)
    ↓
Step 6: Cut (remove duplicate sample name column)
    ↓
checkm_depth_output → Report
```

---

## Step 1 — Search in textfiles (grep)

**Tool:** `Search in textfiles (grep)` (Galaxy Version 9.5+galaxy3)

| Setting | Value |
|---|---|
| **Select lines from** | Shovill log collection |
| **that** | Match |
| **Type of regex** | Perl (-P) |
| **Regular Expression** | `\[shovill\] Estimated sequencing depth:` |
| **Match type** | case insensitive (-i) |
| **Show lines preceding** | 0 |
| **Show lines trailing** | 0 |
| **Output** | text file (for further processing) |

**Expected output per sample:**
```
[shovill] Estimated sequencing depth: 217 x
```

---

## Step 2 — Collapse Collection

**Tool:** `Collapse Collection`

| Setting | Value |
|---|---|
| **Collection of files to collapse** | grep output collection (from Step 1) |
| **Keep one header line** | No |
| **Prepend File name** | Yes ✅ |

**Expected output:**
```
XXXXXXXX_NG_300326_S11_L001_001    [shovill] Estimated sequencing depth: 217 x
XXXXXXXX_SHI_300326_S1_L001_001    [shovill] Estimated sequencing depth: 341 x
```

---

## Step 3 — sed (strip depth label from values)

**Tool:** `Text transformation with sed`

| Setting | Value |
|---|---|
| **File to process** | Collapsed output (from Step 2) |
| **SED Program** | `s/\[shovill\] Estimated sequencing depth: //` |

**Expected output:**
```
XXXXXXXX_NG_300326_S11_L001_001    217 x
XXXXXXXX_SHI_300326_S1_L001_001    341 x
```

---

## Step 4 — sed (insert header line)

**Tool:** `Text transformation with sed`

| Setting | Value |
|---|---|
| **File to process** | Output from Step 3 |
| **SED Program** | `1i name\t[shovill] Estimated sequencing depth:` |
| **Change datatype** | tabular |

**Expected output:**
```
name                                [shovill] Estimated sequencing depth:
XXXXXXXX_NG_300326_S11_L001_001    217 x
XXXXXXXX_SHI_300326_S1_L001_001    341 x
```

---

## Step 5 — Join two Datasets

**Tool:** `Join two Datasets side by side on a specified field` (Galaxy Version 2.1.3)

| Setting | Value |
|---|---|
| **Join** | CheckM output (tabular) |
| **using column** | `1` |
| **with** | Output from Step 4 |
| **and column** | `1` |
| **Keep lines of first input that do not join** | No |
| **Keep lines of first input that are incomplete** | No |
| **Fill empty columns** | No |
| **Keep the header lines** | Yes |

> **Note:** Both input files must be `tabular` datatype. If the depth file is still `txt`, change its datatype to `tabular` in the output settings of Step 4.

> **Note:** The join matches on the sample name in column 1 of both files, ensuring rows are correctly aligned regardless of order.

---

## Step 6 — Cut columns from a table

**Tool:** `Cut columns from a table` (Galaxy Version 1.0.2)

| Setting | Value |
|---|---|
| **Cut columns** | `c1,c2,c3,c4,c5,c6,c7,c8,c9,c10,c11,c12,c13,c14,c16` |
| **Delimited by** | Tab |
| **From** | Join output (from Step 5) |
| **Change datatype** | tabular |
| **Output label** | `checkm_depth_output` |

> **Note:** This skips column 15 (the duplicate sample name from the depth file) and keeps column 16 (the depth value) as the final column.

**Final output:**
```
name                                ...    Completeness    Contamination    Strain heterogeneity    [shovill] Estimated sequencing depth:
XXXXXXXX_NG_300326_S11_L001_001    ...    100.00          0.33             0.00                    217 x
XXXXXXXX_SHI_300326_S1_L001_001    ...    99.70           1.01             12.50                   341 x
```

---

## Report Template

In your Galaxy report, replace:

```
history_dataset_as_table(output="checkm_output", show_column_headers=true)
```

With:

```
history_dataset_as_table(output="checkm_depth_output", show_column_headers=true)
```

---

## QC Requirements (updated section)

```markdown
## Contamination and Completeness Report
### QC Requirements
- Completeness ≥ 95.00
- Contamination ≤ 5.00
- Estimated sequencing depth reported from Shovill assembly log (column 15)
```
