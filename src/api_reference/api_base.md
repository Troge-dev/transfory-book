# BaseTransformer API Reference

## Overview

`BaseTransformer` is the abstract base class for all transformers in **Transfory**. It provides a standard interface for:

* `fit`, `transform`, and `fit_transform` methods  
* Input validation for pandas DataFrames  
* Recording fitted parameters
* Freezing to prevent refitting
* Saving/loading transformer state  
* Optional structured logging via `logging_callback`
* Custom serialization support 

All custom transformers in Transfory should inherit from `BaseTransformer`. 

## Constructor

```python
BaseTransformer(
    name: Optional[str] = None,
    logging_callback: Optional[Callable[[str, Dict[str, Any]], None]] = None
)
```
## Parameters

| Parameter        | Type                                            | Description         |
| ---------------- | ----------------------------------------------- | ------------------------------------------- |
| `name`             | `Optional[str]`             | Human-readable name for the transformer. Defaults to the class name. |
| `logging_callback` | `Optional[Callable[[str, Dict[str, Any]], None]]` | Optional logger function called after `fit` or `transform`.          |


## Properties

| Property        | Type   | Description                                      |
| --------------- | ------ | ------------------------------------------------ |
| `is_fitted`     | `bool` | Returns `True` if transformer has been fitted.   |
| `fitted_params` | `Dict[str, Any]` | Dictionary of parameters learned during fitting. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> BaseTransformer
```
Fits the transformer to the input DataFrame.
- Validates input type
- Calls subclass `_fit()`
- Stores fitted metadata
- Logs the `"fit"` event
- Raises `FrozenTransformerError` if frozen

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Transforms the DataFrame using learned parameters.
- Requires prior fitting
- Validates column consistency
- Calls subclass `_transform()`
- Logs the `"transform"` event
- Raises `NotFittedError` if called before fit

#### `fit_transform`
```python
fit_transform(X: pd.DataFrame, y: Optional[pd.Series] = None) -> pd.DataFrame
```
Convenience method that runs `fit()` followed by `transform()`.

### Freezing Control 

#### `freeze`
```python
freeze() -> None
```
| Behavior | Description                          |
| -------- | ------------------------------------ |
| Effect   | Prevents any future calls to `fit()` |

#### `unfreeze`
```python
unfreeze() -> None
```
| Behavior | Description          |
| -------- | -------------------- |
| Effect   | Allows fitting again |

### Persistence

#### `save`
```python
save(filepath: str) -> None
```
| Feature     | Behavior                              |
| ----------- | ------------------------------------- |
| Storage     | Uses `joblib`                         |
| Directories | Automatically creates missing folders |

#### `load`
```python
load(filepath: str) -> BaseTransformer
```
| Feature     | Behavior                                     |
| ----------- | -------------------------------------------- |
| Type Safety | Ensures loaded object is a `BaseTransformer` |

### Validation

#### `_validate_input`
```python
_validate_input(
    X: pd.DataFrame,
    require_same_columns: bool = False
) -> pd.DataFrame
```
| Check           | Purpose                                       |
| --------------- | --------------------------------------------- |
| DataFrame type  | Ensures input is pandas DataFrame             |
| Column checking | Ensures fitted columns exist during transform |
| Safety          | Returns a shallow copy                        |

### Logging

#### `log`

```python
_log(
    event: str,
    details: Dict[str, Any],
    step_name: Optional[str] = None,
    config: Optional[Dict[str, Any]] = None,
    transformer_name: Optional[str] = None
)
```
| Feature          | Behavior                             |
| ---------------- | ------------------------------------ |
| Safe logging     | Never crashes the pipeline           |
| Auto config      | Extracts public attributes           |
| External support | Used by InsightReporter or pipelines |

### Serialization

#### `__getstate__`
| Feature        | Behavior                                        |
| -------------- | ----------------------------------------------- |
| Logging safety | Excludes `_logging_callback` from serialization |

#### `__setstate__`
| Feature | Behavior                 |
| ------- | ------------------------ |
| Restore | Reloads serialized state |

### Required Subclass Hooks

#### `_fit`
```python
_fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> None
```
| Responsibility                   |
| -------------------------------- |
| Learn parameters                 |
| Store into `self._fitted_params` |

#### `_transform`
```python
_transform(X: pd.DataFrame) -> pd.DataFrame
```
| Responsibility               |
| ---------------------------- |
| Apply learned parameters     |
| Return transformed DataFrame |

### Dunder & Utility Methods
| Method     | Purpose                             |
| ---------- | ----------------------------------- |
| `__repr__` | Shows name, fit status, param keys  |
| `__eq__`   | Equality check                      |
| `__len__`  | Returns number of fitted parameters |

## Exceptions
| Exception              | When Raised                             |
| ---------------------- | --------------------------------------- |
| NotFittedError         | When `transform` is called before `fit` |
| FrozenTransformerError | When `fit` is called after freezing     |
| TypeError              | When input is not a DataFrame           |
| ValueError             | When required columns are missing       |

# ExampleScaler API Reference

## Overview

`ExampleScaler` is a minimal demonstration subclass of BaseTransformer that performs standardization: **x = (x - mean) / std**


## Constructor

```python
​ExampleScaler(
    columns: Optional[Iterable[str]] = None,
    name: Optional[str] = None,
    logging_callback: Optional[Callable[[str, Dict[str, Any]], None]] = None
)
```

## Parameters
| Parameter        | Type                    | Description                                                |
| ---------------- | ----------------------- | ---------------------------------------------------------- |
| columns          | Optional[Iterable[str]] | Columns to scale. If None, all numeric columns are scaled. |
| name             | Optional[str]           | Custom transformer name                                    |
| logging_callback | Optional[callable]      | Optional logger                                            |

## Fitted Parameters
| Key     | Description                   |
| ------- | ----------------------------- |
| means   | Mean per column               |
| stds    | Standard deviation per column |
| columns | Columns selected for scaling  |

## Behavior

#### `_fit`
| Action                                 |
| -------------------------------------- |
| Computes means and standard deviations |

#### `_transform`
| Action                   |
| ------------------------ |
| Applies standard scaling |
