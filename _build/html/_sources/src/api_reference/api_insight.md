# InsightReporter API Reference

## Overview
`InsightReporter` is a lightweight event logger designed to collect and summarize events emitted by transformers in a pipeline. It integrates via the `logging_callback` parameter in transformers and provides human-readable summaries of fitted and transformed steps.

## Constructor

```python
InsightReporter()
```

## Parameters

This class has no constructor parameters.

## Core Public Methods

#### `get_callback`
```python
get_callback() -> Callable[[str, Dict[str, Any]], None]
```
Returns a callable that can be passed to transformers as  `logging_callback`.
Transformers use this callback to report events to the reporter.

#### `log_event`
```python
log_event(step_name: str, payload: Dict[str, Any]) -> None
```
Logs a transformation event with a timestamp.
- `step_name`: Name of the step (usually transformer name).
- `payload`: Dictionary containing event-specific details (e.g., fitted parameters, created columns).

#### `summary`
```python
summary(as_dataframe: bool = False) -> Union[str, pd.DataFrame]
```
Summarizes all logged events in a readable format.
- `as_dataframe`: If `True`, returns a `pandas.DataFrame`; otherwise returns a formatted string.

#### `clear`
```python
clear() -> None
```
Clears all stored logs, resetting the reporter.

#### `export`
```python
export(filepath: str, format: str = "json") -> None
```
Exports logged events to a file.
- `filepath`: Destination file path.
- `format`: `"json"` (default) or `"csv"`.

## Internal / Helper Methods

#### `_format_log_entry`
```python
_format_log_entry(log: Dict[str, Any]) -> str
```
Formats a single log entry into a human-readable explanation.

#### `_format_log_entry_for_transformer`
```python
_format_log_entry_for_transformer(
    step_name: str,
    event: str,
    details: Dict[str, Any],
    display_transformer_name: str,
    logic_transformer_name: str,
    config: Dict[str, Any]
) -> str
```
Generates custom explanation messages for specific transformers and event types.

#### `_get_fitted_columns`
```python
_get_fitted_columns(details: Dict[str, Any], param_key: str) -> List[str]
```
Extracts fitted column names from a log's details dictionary.

## Dunder & Utility Methods

#### `repr`
```python
__repr__() -> str
```
Returns a string summarizing the reporter, including the number of logged events.

## Example Usage
```python
from transfory import InsightReporter, ExampleScaler
import pandas as pd

df = pd.DataFrame({
    'x1': [1, 2, 3],
    'x2': [4, 5, 6]
})

reporter = InsightReporter()
callback = reporter.get_callback()

scaler = ExampleScaler(logging_callback=callback)
scaler.fit_transform(df)

print(reporter.summary())
reporter.export("log.json")
```

## Notes
- Handles nested transformers, such as `ColumnTransformer`, by prefixing sub-step names in logs.
- Provides custom messages for all main transformers (e.g., `MissingValueHandler`, `Encoder`, `FeatureGenerator`, `DatetimeFeatureExtractor`, `OutlierHandler`).
- Logs include timestamps for event tracking and reproducibility.
