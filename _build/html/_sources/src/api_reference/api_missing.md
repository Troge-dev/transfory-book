# MissingValueHandler API Reference

## Overview
`MissingValueHandler` is a transformer that fills missing values in a `pandas.DataFrame` using different strategies. It can handle numeric and categorical data and supports constant values for imputation.

### Strategies
- `mean`: Fills missing numeric values with the column mean.  
- `median`: Fills missing numeric values with the column median.  
- `mode`: Fills missing numeric or categorical values with the mode.  
- `constant`: Fills missing values with a user-provided `fill_value`.

## Constructor

```python
MissingValueHandler(strategy: str = "mean", fill_value: Any = None, name: Optional[str] = None)
```
## Parameters

| Parameter  | Type | Description |
| ---------- | -----| ----------- |
| strategy   | str  | The imputation strategy to use. Options: `'mean'`, `'median'`, `'mode'`, `'constant'`. Defaults to `'mean'`. |
| fill_value | Any  | Required if `strategy='constant'`. Value to fill missing entries. |
| name       | str, optional | Custom name for the transformer instance. Defaults to `"MissingValueHandler(strategy='...')"` |

## Fitted Parameters

| Parameter     | Description                                                                |
| ------------- | -------------------------------------------------------------------------- |
| `fill_values` | Dictionary of column-specific imputation values calculated during fitting. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> MissingValueHandler
```
Calculates imputation values for each column according to the selected strategy and stores them in `_fitted_params`.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Applies the stored imputation values to fill missing values in the DataFrame.

#### `fit_transform`
Convenience method that runs `fit` followed by `transform`.

## Dunder & Utility Methods

#### `repr`
```python
__repr__() -> str
```
Returns a string representing the transformer and its configuration.

## Example Usage
```python
import pandas as pd
from transfory import MissingValueHandler

df = pd.DataFrame({
    "age": [25, None, 30],
    "sex": ["M", "F", None]
})

# Fill missing numeric values with mean, and categorical values with mode
imputer = MissingValueHandler(strategy="mode")
imputer.fit_transform(df)
```

## Notes
- Automatically selects numeric or categorical handling depending on the strategy.
- `constan`t strategy requires `fill_value`.
- Stores fitted values in `_fitted_params["fill_values"]` for logging, persistence, or reporting.
