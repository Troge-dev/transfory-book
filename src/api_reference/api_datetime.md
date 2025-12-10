# DatetimeFeatureExtractor API Reference

## Overview
`DatetimeFeatureExtractor` is a subclass of `BaseTransformer` that extracts date and time features from datetime columns in a pandas DataFrame. It can automatically detect datetime-like columns or process user-specified columns. Extracted features are added as new columns, and the original datetime columns are dropped.

## Constructor

```python
DatetimeFeatureExtractor(
    features: Optional[List[str]] = None,
    columns: Optional[List[str]] = None,
    name: Optional[str] = None
)
```

## Parameters
| Parameter  | Type                | Description                                                                                                                                                                                                                                            |
| ---------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `features` | Optional[List[str]] | List of datetime attributes to extract. Defaults to `['year', 'month', 'day', 'dayofweek']`. Supported features include any valid pandas `.dt` accessor attribute (e.g., `'year'`, `'month'`, `'day'`, `'hour'`, `'minute'`, `'dayofweek'`, `'week'`). |
| `columns`  | Optional[List[str]] | Specific columns to process. If None, all object or datetime64 columns are considered.                                                                                                                                                                 |
| `name`     | Optional[str]       | Optional human-readable name for the transformer. Defaults to `"DatetimeFeatureExtractor"`.                                                                                                                                                            |

## Fitted Parameters
| Key                | Description                                                                      |
| ------------------ | -------------------------------------------------------------------------------- |
| `datetime_columns` | List of columns identified as datetime-like and processed during transformation. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> DatetimeFeatureExtractor
```
Fits the transformer to the input DataFrame.
- Validates input type.
- Calls `_fit()` internally.
- Records datetime columns to extract features from.
- Logs the `"fit"` event.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Transforms the DataFrame by extracting the specified datetime features.
- Requires prior fitting.
- Calls `_transform()` internally.
- Adds new columns for each extracted feature.
- Drops original datetime columns.
- Logs the `"transform"` event with details such as input/output shape and new columns created.
- Raises `NotFittedError` if called before `fit`.

#### `fit_transform`
```python
fit_transform(X: pd.DataFrame, y: Optional[pd.Series] = None) -> pd.DataFrame
```
Convenience method that runs `fit()` followed by `transform()`.

## Required Subclass Hooks

#### `_fit`
```python
_fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> None
```
- Identifies datetime-like columns based on columns parameter or auto-detection.
- Stores results in `self._fitted_params["datetime_columns"]`.

#### `_transform`
```python
_transform(X: pd.DataFrame) -> pd.DataFrame
```
- Extracts datetime features from the fitted columns.
- Drops the original datetime columns.
- Returns the transformed DataFrame.

## Dunder & Utility Methods

#### `repr`
```python
__repr__() -> str
```
Shows the class name, fit status, and key fitted parameters.


## Example Usage

```python
from transfory import DatetimeFeatureExtractor
import pandas as pd

df = pd.DataFrame({
    'date': pd.to_datetime(['2023-01-01', '2023-02-15', '2023-03-20'])
})

extractor = DatetimeFeatureExtractor(features=['year','month','day'], columns=['date'])
df_transformed = extractor.fit_transform(df)
print(df_transformed)
```
