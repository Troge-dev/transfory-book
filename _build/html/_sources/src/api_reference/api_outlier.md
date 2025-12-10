# OutlierHandler API Reference

## Overview
`OutlierHandler` is a transformer that detects and caps outliers in numerical columns of a `pandas.DataFrame`. Outliers can be handled either using the Interquartile Range (IQR) method or by percentile capping.

### Methods
- `iqr`: Caps values below `Q1 - factor * IQR` or above `Q3 + factor * IQR`.  
- `percentile`: Caps values at the specified lower and upper percentiles.

## Constructor

```python
OutlierHandler(method: str = "iqr", factor: float = 1.5,
               lower_quantile: float = 0.01, upper_quantile: float = 0.99,
               quantile_interpolation: str = "linear",
               columns: Optional[List[str]] = None,
               name: Optional[str] = None)
```

## Parameters

| Parameter              | Type                  | Description                                                                                                  |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------ |
| method                 | str                   | Method to detect outliers. Options: `'iqr'` or `'percentile'`. Default: `'iqr'`.                             |
| factor                 | float                 | Multiplier for the IQR when using the `'iqr'` method. Must be positive. Default: `1.5`.                      |
| lower_quantile         | float                 | Lower percentile for capping when using the `'percentile'` method. Must be between 0 and 1. Default: `0.01`. |
| upper_quantile         | float                 | Upper percentile for capping when using the `'percentile'` method. Must be between 0 and 1. Default: `0.99`. |
| quantile_interpolation | str                   | Method of interpolation for pandas `.quantile()` function. Default: `'linear'`.                              |
| columns                | list of str, optional | Specific numeric columns to process. If None, all numeric columns are used.                                  |
| name                   | str, optional         | Custom name for the transformer instance. Defaults to `"OutlierHandler(method='...')"`                       |

## Fitted Parameters

| Parameter | Description                                                                                      |
| --------- | ------------------------------------------------------------------------------------------------ |
| `bounds`  | Dictionary mapping column names to `(lower_bound, upper_bound)` tuples, computed during fitting. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> OutlierHandler
```
Calculates the bounds for each column based on the selected method and stores them in _fitted_params.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Caps the values in the DataFrame according to the stored bounds.

#### `fit_transform`
Convenience method that runs `fit` followed by `transform`.

## Example Usage
```python
import pandas as pd
from transfory import OutlierHandler

df = pd.DataFrame({
    "age": [25, 120, 30, -5, 40],
    "salary": [50000, 1000000, 55000, 60000, 52000]
})

# Cap outliers using IQR method
handler = OutlierHandler(method="iqr", factor=1.5)
df_capped = handler.fit_transform(df)
```
## Notes
- The `iqr` method is suitable for normally distributed data.
- The `percentile` method is suitable for skewed distributions.
- Numeric columns are automatically detected if `columns` is None.
- Fitted bounds are stored in `_fitted_params["bounds"]` for logging or inspection.
