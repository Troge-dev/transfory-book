# FeatureGenerator API Reference

## Overview
`FeatureGenerator` is a subclass of `BaseTransformer` that creates polynomial and interaction features from numeric columns in a DataFrame. This transformer is useful for expanding feature spaces in machine learning pipelines.

## Constructor

```python
FeatureGenerator(
    degree: int = 2,
    include_interactions: bool = True,
    name: Optional[str] = None,
    logging_callback: Optional[Callable[[str, dict], None]] = None
)
```

## Parameters

| Parameter | Type     | Description |
| --------- | ---------| ----------- |
| `degree`  | int      | Maximum degree for polynomial features. For example, `degree=3` generates x² and x³ features. Default is 2. |
| `include_interactions` | bool | Whether to include interaction terms (pairwise products of numeric columns). Default is True. |
| `name` | Optional[str] | Optional human-readable name for the transformer. Defaults to `"FeatureGenerator(degree=X)"`. |
| `logging_callback`  | Optional[callable] | Optional logging function that receives events and details during transform. |

## Fitted Parameters

| Key                  | Description                                              |
| -------------------- | -------------------------------------------------------- |
| `columns_to_process` | List of numeric columns selected for feature generation. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> FeatureGenerator
```
Fits the transformer to the DataFrame.
- Selects numeric columns to process.
- Stores column list in `self._fitted_params["columns_to_process"]`.
- Logs `"fit"` event.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Transforms the DataFrame by generating new features.
- Polynomial features: x², x³, ..., up to `degree`.
- Interaction terms: all pairwise products of numeric columns (if `include_interactions=True`).
- Logs `"transform"` event with details:
    - Input shape
    - Output shape
    - List of new features created
- Returns the transformed DataFrame.

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
- Determines which numeric columns will be used for feature generation.
- Stores result in `self._fitted_params["columns_to_process"]`.

#### `_transform`
```python
_transform(X: pd.DataFrame) -> pd.DataFrame
```
- Generates polynomial and interaction features for numeric columns.
- Returns transformed DataFrame with new features added.

## Dunder & Utility Methods

#### `repr`
```python
__repr__() -> str
```
Returns a string representation showing `degree` and whether interaction terms are included.

## Example Usage

```python
import pandas as pd
from transfory import FeatureGenerator

df = pd.DataFrame({
    'x1': [1, 2, 3],
    'x2': [4, 5, 6],
    'y': [7, 8, 9]
})

# Generate polynomial features and interactions
fg = FeatureGenerator(degree=3, include_interactions=True)
df_transformed = fg.fit_transform(df)
print(df_transformed)
```
