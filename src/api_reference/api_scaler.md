# Scaler API Reference

## Overview
`Scaler` is a wrapper around scikit-learn's scaling transformers that applies a scaling method to all numeric columns in a `pandas.DataFrame`.

### Key Features
- Supports Min-Max scaling and Z-score standardization.
- Automatically detects numeric columns.
- Compatible with other transformers in pipelines.
- Stores fitted scaler and column information for inspection or logging.

## Constructor

```python
Scaler(method="minmax")
```
## Parameters
| Parameter | Type | Description                                                                                           |
| --------- | ---- | ----------------------------------------------------------------------------------------------------- |
| method    | str  | Scaling method. Options: `'minmax'` (MinMaxScaler), `'zscore'` (StandardScaler). Default: `'minmax'`. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y=None) -> None
```
Fit the scaler to all numeric columns in the DataFrame.

#### `transform`
```python
Fit the scaler to all numeric columns in the DataFrame.
```
Apply the fitted scaler to transform all numeric columns.

#### `fit_transform`
```python
fit_transform(X: pd.DataFrame, y=None) -> pd.DataFrame
```
Convenience method to fit and transform in one step.

## Example Usage
```python
import pandas as pd
from transfory.scaler import Scaler

df = pd.DataFrame({
    "age": [25, 30, 22, 28],
    "income": [50000, 60000, 45000, 52000],
    "city": ["NY", "LA", "SF", "NY"]
})

scaler = Scaler(method="zscore")
scaler.fit(df)
df_scaled = scaler.transform(df)

# Or in one step
df_scaled = scaler.fit_transform(df)
```

## Notes
- Only numeric columns are scaled; non-numeric columns are left unchanged.
- Fitted scaler and columns are stored in `_fitted_params` for logging, inspection, or persistence.
- an be integrated seamlessly in a `Pipeline` with other transformers.
