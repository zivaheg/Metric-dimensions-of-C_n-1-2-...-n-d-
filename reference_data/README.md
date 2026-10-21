# Legacy reference data

## `reference20_metric_dimensions.csv`

This file is a mechanical wide-to-long conversion of `rezultati_naloga_2.xlsx` from:

<https://github.com/JasminaMazej/projektna-naloga-FP->

Source details:

```text
commit: c7e4391e95132212add8eb3bd9ddb4d8f2e17f43
source file: rezultati_naloga_2.xlsx
source SHA-256: EF8B58B0CA31B89823AFE661119B6B04D2D4B606329611CE2FE319632B1B96BD
```

Transformation:

- the source sheets `dimenzija za m=4` and `dimenzija za m=5-10` were combined;
- source parameter `m` was renamed to `d`, because the notebook constructs `C_n(1,...,n-m)`;
- only rows satisfying the current assignment's condition `n>2d` were retained;
- the numeric values were not changed;
- output columns are `n,d,dimension`, sorted by `d` and then `n`.

The file has 462 data rows covering `4<=d<=10`, with maximum `n=80`.

Validation performed at extraction time:

- all `d=4` rows agree with `ceil(2n/5)`;
- all `d=5` rows agree with the piecewise formula in the current assignment;
- all rows satisfy the lower bound in Conjecture 1;
- all rows agree with the conjecture's claimed equality/divisibility characterization.

Important limitation: the source workbook stores dimensions but not resolving sets, solver status, solver/backend version, runtime, or code revision per row. Treat these values as legacy comparison data. Do not merge them into newly generated exact results unless the new solver independently reproduces them and its certificate checker validates the returned resolving sets.

