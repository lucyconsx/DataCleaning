# INGREDIENT DATA CLEANING PIPELINE
 
A Jupyter Notebook pipeline for standardizing and validating messy client
ingredient upload files. Built for cosmetics/formulation contexts where data
quality across supplier records, INCI names, and concentration values directly
affects compliance and downstream processing.

 
 
DATASET
-------
RawMessyData.xlsx
  Raw client upload with two sheets: Client_Ingredient_Upload and
  Supplier_Reference
 
MessyData_CLEAN.xlsx
  Output file produced by the notebook
 
 
ISSUES ADDRESSED
----------------
 1  Exact duplicate rows
    Approach: drop_duplicates() after load
 
 2  Near-duplicates from casing differences
    Approach: dedup on ingredient_id after casing is standardized
 
 3  Inconsistent text casing
    Approach: .str.title() for names/functions, .str.upper() for INCI
    (standard convention)
 
 4  Extra whitespace
    Approach: .str.strip() across all string columns
 
 5  Missing required fields
    Approach: flagged and printed, not dropped
 
 6  Wrong data type (concentration_pct)
    Approach: strip %, cast to float
 
 7  Out-of-range concentration values
    Approach: flagged, set to pd.NA for manual review
 
 8  Typos in controlled vocabulary
    Approach: explicit STATUS_MAP dict; unmapped values surface as
    "UNKNOWN - review required"
 
 9  Mixed date formats
    Approach: pd.to_datetime(errors="coerce") -> ISO 8601 (YYYY-MM-DD)
 
10  Supplier name inconsistency across sheets
    Approach: SUPPLIER_MAP dict applied to both tabs
 
11  Supplier code bleeding into ingredient name
    Approach: regex strips leading XXX-#### patterns
    e.g. "BRN-9920 Shea Butter" -> "Shea Butter"
 
 
NOTEBOOK STRUCTURE
------------------
 01  Imports & file path
 02  Load data             raw_* copies preserved for diffing
 03  Column names          snake_case normalization
 04  String cleaning       whitespace, casing, supplier code bleed
 05  Controlled vocab      safety_status typo correction
 06  Data types            concentration_pct to float
 07  Out-of-range          concentration validation (0-100)
 08  Date normalization    ISO 8601 standardization
 09  Supplier mapping      cross-sheet name consistency
 10  Missing fields        required field audit
 11  Duplicates            exact then near-duplicate detection
 12  Validation summary    dtypes, null counts, preview
 13  Export                write to MessyData_CLEAN.xlsx
 
 
DESIGN PRINCIPLES
-----------------
Flag, don't drop.
  Out-of-range values and near-duplicates are printed for human review
  rather than silently removed.
 
Preserve raw data.
  raw_ing and raw_sup are kept untouched so you can diff against the
  cleaned output at any point.
 
One issue per cell.
  Each cleaning step is isolated so individual steps can be re-run
  without re-executing the full pipeline.
 
Explicit vocabulary maps.
  STATUS_MAP and SUPPLIER_MAP are defined as named dicts at the top of
  their cells - easy to extend as new variants appear.
 
 
REQUIREMENTS
------------
  pandas
  openpyxl
 
Install with:
  pip install pandas openpyxl
 
Built and tested with pandas 2.x. The infer_datetime_format argument has
been removed from the date cell as it is deprecated in pandas 2.0+.
 
 
USAGE
-----
1. Place RawMessyData.xlsx in the same directory as the notebook
2. Run all cells top to bottom
3. Review any flagged rows printed to output
4. Collect cleaned output from MessyData_CLEAN.xlsx
 
 
SKILLS DEMONSTRATED
-------------------
  - pandas data wrangling (dtype casting, regex, method chaining)
  - Data quality auditing and validation logic
  - Controlled vocabulary management
  - Cross-sheet consistency enforcement
  - Jupyter Notebook documentation structure
 
 
AUTHOR
------
Lucy - Spreadsheeter ATX
