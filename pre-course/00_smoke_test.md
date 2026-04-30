# 00 Smoke Test

# Environment Verification - Smoke Test

This notebook verifies that your Python environment is correctly set up for the **Marketing Science Bootcamp**.

Run every cell from top to bottom. At the end you will see a summary telling you whether all checks passed or if something needs fixing.

---

## 1. Python Version

```python
import sys
print(f"Python version: {sys.version}")
```

## 2. Package Import Checks

Each cell below attempts to import a required package and reports **PASS** or **FAIL**.

```python
# We will collect results here
results = []
```

```python
try:
    import pandas as pd
    print(f"PASS  -  pandas {pd.__version__}")
    results.append(("pandas", True, pd.__version__))
except ImportError as e:
    print(f"FAIL  -  pandas: {e}")
    results.append(("pandas", False, str(e)))
```

```python
try:
    import numpy as np
    print(f"PASS  -  numpy {np.__version__}")
    results.append(("numpy", True, np.__version__))
except ImportError as e:
    print(f"FAIL  -  numpy: {e}")
    results.append(("numpy", False, str(e)))
```

```python
try:
    import matplotlib
    print(f"PASS  -  matplotlib {matplotlib.__version__}")
    results.append(("matplotlib", True, matplotlib.__version__))
except ImportError as e:
    print(f"FAIL  -  matplotlib: {e}")
    results.append(("matplotlib", False, str(e)))
```

```python
try:
    import seaborn as sns
    print(f"PASS  -  seaborn {sns.__version__}")
    results.append(("seaborn", True, sns.__version__))
except ImportError as e:
    print(f"FAIL  -  seaborn: {e}")
    results.append(("seaborn", False, str(e)))
```

```python
try:
    import scipy
    print(f"PASS  -  scipy {scipy.__version__}")
    results.append(("scipy", True, scipy.__version__))
except ImportError as e:
    print(f"FAIL  -  scipy: {e}")
    results.append(("scipy", False, str(e)))
```

```python
try:
    import statsmodels
    print(f"PASS  -  statsmodels {statsmodels.__version__}")
    results.append(("statsmodels", True, statsmodels.__version__))
except ImportError as e:
    print(f"FAIL  -  statsmodels: {e}")
    results.append(("statsmodels", False, str(e)))
```

```python
try:
    import pymc
    print(f"PASS  -  pymc {pymc.__version__}")
    results.append(("pymc", True, pymc.__version__))
except ImportError as e:
    print(f"FAIL  -  pymc: {e}")
    results.append(("pymc", False, str(e)))
```

```python
try:
    import arviz as az
    print(f"PASS  -  arviz {az.__version__}")
    results.append(("arviz", True, az.__version__))
except ImportError as e:
    print(f"FAIL  -  arviz: {e}")
    results.append(("arviz", False, str(e)))
```

```python
try:
    import pytensor
    print(f"PASS  -  pytensor {pytensor.__version__}")
    results.append(("pytensor", True, pytensor.__version__))
except ImportError as e:
    print(f"FAIL  -  pytensor: {e}")
    results.append(("pytensor", False, str(e)))
```

```python
try:
    import pymc_marketing
    print(f"PASS  -  pymc_marketing {pymc_marketing.__version__}")
    results.append(("pymc_marketing", True, pymc_marketing.__version__))
except ImportError as e:
    print(f"FAIL  -  pymc_marketing: {e}")
    results.append(("pymc_marketing", False, str(e)))
```

```python
try:
    import sklearn
    print(f"PASS  -  sklearn {sklearn.__version__}")
    results.append(("sklearn", True, sklearn.__version__))
except ImportError as e:
    print(f"FAIL  -  sklearn: {e}")
    results.append(("sklearn", False, str(e)))
```

```python
try:
    import networkx as nx
    print(f"PASS  -  networkx {nx.__version__}")
    results.append(("networkx", True, nx.__version__))
except ImportError as e:
    print(f"FAIL  -  networkx: {e}")
    results.append(("networkx", False, str(e)))
```

```python
try:
    import openpyxl
    print(f"PASS  -  openpyxl {openpyxl.__version__}")
    results.append(("openpyxl", True, openpyxl.__version__))
except ImportError as e:
    print(f"FAIL  -  openpyxl: {e}")
    results.append(("openpyxl", False, str(e)))
```

```python
try:
    import jupyter
    print(f"PASS  -  jupyter (meta-package imported successfully)")
    results.append(("jupyter", True, "OK"))
except ImportError as e:
    print(f"FAIL  -  jupyter: {e}")
    results.append(("jupyter", False, str(e)))
```

## 3. Data File Check

Verify that the workshop Excel file can be loaded.

```python
data_pass = False
try:
    import pandas as pd
    data = pd.read_excel("../data/MMM_Workshop_Data.xlsx", sheet_name="Data")
    print(f"PASS  -  Workshop data loaded successfully: {data.shape[0]} rows x {data.shape[1]} columns")
    print(f"         Columns: {list(data.columns)}")
    data_pass = True
except FileNotFoundError:
    print("FAIL  -  File not found: ../data/MMM_Workshop_Data.xlsx")
    print("         Make sure the data folder is in the expected location.")
except Exception as e:
    print(f"FAIL  -  Could not load data: {e}")
```

## 4. Summary

```python
import sys

print("=" * 60)
print("ENVIRONMENT VERIFICATION SUMMARY")
print("=" * 60)
print(f"\nPython version : {sys.version}")
print(f"Platform       : {sys.platform}\n")

print("-" * 60)
print(f"{'Package':<20} {'Status':<10} {'Version / Error'}")
print("-" * 60)

all_passed = True
for pkg, ok, info in results:
    status = "PASS" if ok else "FAIL"
    if not ok:
        all_passed = False
    print(f"{pkg:<20} {status:<10} {info}")

data_status = "PASS" if data_pass else "FAIL"
if not data_pass:
    all_passed = False
print(f"{'data file':<20} {data_status:<10} {'../data/MMM_Workshop_Data.xlsx'}")

print("-" * 60)
print()

if all_passed:
    print("\033[92m" + "=" * 60)
    print("  ALL CHECKS PASSED! Your environment is ready.")
    print("=" * 60 + "\033[0m")
else:
    failed = [pkg for pkg, ok, _ in results if not ok]
    if not data_pass:
        failed.append("data file")
    print("\033[91m" + "=" * 60)
    print("  WARNING: Some checks FAILED!")
    print(f"  Failed items: {', '.join(failed)}")
    print("  Please fix the issues above before Session 1.")
    print("=" * 60 + "\033[0m")
```

