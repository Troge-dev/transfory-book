# Pipeline API Reference

## Overview
`Pipeline` is a transformer container that sequentially applies multiple transformers to a `pandas.DataFrame`. Each step processes the output of the previous one. It supports logging and nested callback reporting.

### Key Features
- Chain multiple transformers.
- Nested logging via `InsightReporter`.
- Step management: add, remove, retrieve steps.
- Persistence: save and load entire pipelines.
- Efficient `fit_transform` implementation to avoid redundant transforms.

## Constructor

```python
Pipeline(steps: List[Tuple[str, BaseTransformer]], 
         name: Optional[str] = None,
         logging_callback: Optional_
```
## Parameters
| Parameter | Type | Description |
| ----------| ---- | ----------- |
| steps     | List[Tuple[str, BaseTransformer]] | List of `(name, transformer)` tuples. Transformers must inherit from `BaseTransformer`. |
| name      | str, optional | Custom name for the pipeline. Default: `"Pipeline"`. |
| logging_callback | callable, optional | Function to capture logs from transformers. Usually obtained from `InsightReporter.get_callback()`. |

## Core Public Methods

#### `fit`
```python
fit(X: pd.DataFrame, y: Optional[pd.Series] = None) -> None
```
Sequentially fits each transformer on the data. Logging is performed for each step.

#### `transform`
```python
transform(X: pd.DataFrame) -> pd.DataFrame
```
Applies the transformations sequentially on the input DataFrame.

#### `fit_transform`
```python
fit_transform(X: pd.DataFrame, y: Optional[pd.Series] = None) -> pd.DataFrame
```
Convenience method that fits all transformers and returns the transformed DataFrame in a single pass.

#### `add_step`
```python
add_step(name: str, transformer: BaseTransformer) -> None
```
Add a new transformer to the end of the pipeline.

#### `remove_step`
```python
remove_step(name: str) -> None
```
Remove a transformer from the pipeline by name.

#### `get_step`
```python
get_step(name: str) -> Optional[BaseTransformer]
```
Retrieve a transformer by its name.

#### `save`
```python
save(filepath: str) -> None
```
Persist the pipeline (including all steps) to disk using `joblib`.

#### `load`
```python
Pipeline.load(filepath: str) -> Pipeline
```
Load a saved pipeline from disk.

## Example Usage
```python
import pandas as pd
from transfory.pipeline import Pipeline
from transfory.encoder import Encoder
from transfory.imputer import Imputer
from transfory.scaler import ExampleScaler

df_train = pd.DataFrame({
    "age": [25, None, 30, 22],
    "city": ["NY", "LA", None, "SF"]
})

df_test = pd.DataFrame({
    "age": [28, 35],
    "city": ["LA", "NY"]
})

pipe = Pipeline([
    ("imputer", Imputer(strategy="mean")),
    ("encoder", Encoder(method="label")),
    ("scaler", ExampleScaler())
])

# Fit pipeline on training data
pipe.fit(df_train)

# Transform test data
df_transformed = pipe.transform(df_test)
```

## Notes
- `Pipeline` automatically validates that each step is a valid `BaseTransformer`.
- Logging is hierarchical: nested step names are represented as `"pipeline_step::child_step"`.
- Use `InsightReporter` to capture and summarize the transformations performed at each step.
- `fit_transform` is optimized to avoid unnecessary recomputation at the final step.
