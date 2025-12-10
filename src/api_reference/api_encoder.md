# Encoder API Reference

## Overview  
`Encoder` is a categorical data transformer that converts string or categorical columns into numerical representations using either **label encoding** or **one-hot encoding**. It inherits from `BaseTransformer` and follows the standard `fit → transform` workflow.

## Constructor  

```python
Encoder(
    method="onehot",
    handle_unseen="ignore",
    name=None
)
```
## Parameters

| Parameter  | Type | Default    | Description |
| ---------  | ---- | ---------- | --------|
| `method`   | `str` | `"onehot"` | Encoding method. Options: `"label"`, `"onehot"`.          |
| `handle_unseen` | `str` | `"ignore"` | How to handle unseen categories during transform. Options: `"ignore"`, `"error"`.|
| `name` | `str` or `None` | `None`| Optional custom name of the transformer. |

## Fitted Parameters
| Key    | Description |
| -------| ----------- |
| `mappings` | Dictionary mapping column names to either label mapping (`dict`) or list of unique categories (`list`) depending on the encoding method. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> Encoder
```
Fits the encoder to the DataFrame.
- Determines unique categories per categorical column.
- Stores mappings in `self._fitted_params["mappings"]`.
- Logs the `"fit"` event.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Transforms the DataFrame using the fitted encoding.
- **Label Encoding:**
    - Maps known categories to integers.
    - Handles unseen values based on handle_unseen.
- **One-hot Encoding:**
    - Creates a binary column for each category in each column.
    - Drops original categorical columns.
    - Handles unseen values based on `handle_unseen`.
- Logs the `"transform"` event with details like input/output shape and new columns added.
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
- Computes unique categories for each categorical column.
- Populates `self._fitted_params["mappings"]`.

#### `_transform`
```python
_transform(X: pd.DataFrame) -> pd.DataFrame
```
- Applies label or one-hot encoding using the fitted mappings.
- Handles unseen categories according to `handle_unseen`.
- Returns the transformed DataFrame.

## Dunder & Utility Methods

#### `repr`
```python
__repr__() -> str
```
Returns a string representation showing the encoding method and unseen category strategy.

## Example Usage

```python
import pandas as pd
from transfory import Encoder

df = pd.DataFrame({
    'color': ['red', 'blue', 'green', 'red'],
    'shape': ['circle', 'square', 'triangle', 'circle']
})

# One-hot encoding
encoder = Encoder(method='onehot')
df_encoded = encoder.fit_transform(df)
print(df_encoded)

# Label encoding
encoder_label = Encoder(method='label')
df_label_encoded = encoder_label.fit_transform(df)
print(df_label_encoded)
```
