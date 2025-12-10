# ColumnTransformer API Reference

## Overview

`ColumnTransformer` is a composite transformer that applies **different transformers to different columns** of a pandas DataFrame. It enables:

- Parallel column-wise transformations
- Mixing transformed and passthrough columns
- Automatic numeric and categorical column selection
- Callable-based column selection
- Remainder column handling (`drop` or `passthrough`)
- Integrated structured logging via `BaseTransformer`

It is inspired by scikit-learn’s `ColumnTransformer`, but designed to work natively with **Transfory transformers**.

## Constructor

```python
ColumnTransformer(
    transformers: List[Tuple[str, Union[BaseTransformer, str], Union[str, List[str], Callable]]],
    remainder: str = 'drop',
    name: Optional[str] = None,
    logging_callback: Optional[Callable[[st]()]()]()_
```

## Parameters

| Parameter          | Type                                                                        | Description                                                                |
| ------------------ | --------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| `transformers`     | `List[Tuple[str, BaseTransformer or str, Union[str, List[str], Callable]]]` | List of `(name, transformer, columns)` tuples.                             |
| `remainder`        | `{'drop', 'passthrough'}`                                                   | How to handle columns not assigned to any transformer.                     |
| `name`             | `Optional[str]`                                                             | Human-readable name of the transformer. Defaults to `"ColumnTransformer"`. |
| `logging_callback` | `Optional[Callable]`                                                        | Optional structured logging callback.                                      |

## Transformers Argument Format

Each transformer tuple must follow:
```python
(name, transformer, columns)
```
#### `name`
- `str`
- Unique identifier for the sub-transformer

#### `transformer`
- `BaseTransformer` instance.
- `"passthrough"` to include columns unchanged.

#### `columns`
Can be one of:
| Type            | Behavior                                              |
| --------------- | ----------------------------------------------------- |
| `List[str]`     | Selects explicit column names                         |
| `"numeric"`     | Selects all numeric columns                           |
| `"categorical"` | Selects all object & category columns                 |
| `Callable`      | Function that receives `X.columns` and returns a list |

## Properties
| Property        | Type             | Description                                   |
| --------------- | ---------------- | --------------------------------------------- |
| `is_fitted`     | `bool`           | Returns `True` if transformer has been fitted |
| `fitted_params` | `Dict[str, Any]` | Stores learned transformation metadata        |

## Fitted Parameters
| Key                      | Description                                          |
| ------------------------ | ---------------------------------------------------- |
| `processed_transformers` | List of `(name, fitted_transformer, actual_columns)` |
| `passthrough_columns`    | Explicitly passed-through columns                    |
| `remainder_columns`      | Columns not selected by any transformer              |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> ColumnTransformer
```
Fits all sub-transformers on their respective column subsets.
| Behavior |
| -------- |
| Resolves column selectors |
| Deep-copies transformers for isolation |
| Fits each transformer independently |
| Stores fitted transformer metadata |
| Tracks passthrough and remainder columns |
| Logs all sub-transformer operations |
| Raises `FrozenTransformerError` if frozen |

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Applies all fitted transformers and concatenates results.
| Behavior |
| -------- |
| Requires prior fitting |
| Applies each transformer to its columns |
| Adds passthrough columns |
| Adds remainder columns if enabled |
| Preserves row index |
| Concatenates all outputs column-wise |
| Raises NotFittedError if called before fitting |

#### `fit_transform`
```python
fit_transform(X: pd.DataFrame, y: Optional[pd.Series] = None) -> pd.DataFrame
```
Runs `fit()` followed by `transform()`.

## Internal Helper Methods

#### `_get_columns_by_selector`
```python
_get_columns_by_selector(
    X: pd.DataFrame,
    selector: Union[str, List[str], Callable]
) -> List[str]
```
Resolves column selectors into actual column names.
| Selector Type   | Behavior                           |
| --------------- | ---------------------------------- |
| `list`          | Filters valid column names         |
| `"numeric"`     | Selects numeric columns            |
| `"categorical"` | Selects object & category columns  |
| `callable`      | Must return a list of column names |

## Required Subclass Hooks (from BaseTransformer)

#### `_fit`
```python
_fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> None
```
| Responsibility                |
| ----------------------------- |
| Fits each sub-transformer     |
| Tracks selected columns       |
| Stores processed transformers |

#### `_transform`
```python
_transform(X: pd.DataFrame) -> pd.DataFrame
```
| Responsibility                       |
| ------------------------------------ |
| Applies each fitted transformer      |
| Appends passthrough columns          |
| Appends remainder columns            |
| Returns final concatenated DataFrame |

## Logging Behavior
`ColumnTransformer` automatically logs:
| Event                             | Trigger                         |
| --------------------------------- | ------------------------------- |
| `fit_sub_transformer_start`       | Before fitting each transformer |
| `fit_sub_transformer_end`         | After fitting each transformer  |
| `transform_sub_transformer_start` | Before transforming             |
| `transform_sub_transformer_end`   | After transforming              |
| `transform_passthrough`           | When explicit passthrough runs  |
| `transform_remainder`             | When remainder passthrough runs |

All sub-transformer logs are namespaced as:
```text
<group_name>::<step_name>
```

## Dunder & Utility Methods
| Method     | Purpose                                |
| ---------- | -------------------------------------- |
| `__repr__` | Shows transformer count and fit status |

Example output:
```text
<ColumnTransformer (3 transformers) status=fitted>
```

## Exceptions
| Exception        | When Raised                                 |
| ---------------- | ------------------------------------------- |
| `NotFittedError` | When `transform()` is called before fitting |
| `ValueError`     | Invalid transformer format or selectors     |
| `TypeError`      | Invalid selector type                       |

## Example Usage
```python
ct = ColumnTransformer(
    transformers=[
        ("num", scaler, "numeric"),
        ("cat", encoder, "categorical"),
        ("id", "passthrough", ["user_id"])
    ],
    remainder="drop"
)

X_out = ct.fit_transform(X)
```
