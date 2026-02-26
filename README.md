# DTPC

**D**eterministic **T**ensor **P**re/**P**ost **C**ompiler

Compiles small Python tensor pipelines into C with a stable C ABI and deterministic float behavior.

## Usage

You write a function and mark it with `@compile`:

```python
@compile
def preprocess(x: Tensor[float32, (N,)]):
    y = (x - mean) / std
    y = clip(y, -3.0, 3.0)
    return y
```

## Supported operations

- **Arithmetic:** `+`, `-`, `*`, `/`
- **Ufuncs:** `sqrt`, `exp`, `log`, `abs`
- **Other:** `clip`, `reshape`, `sum` / `mean` (per axis), `concat`, `cast`
- **Broadcasting:** NumPy-style rules

## Output

- **Artifacts:** header + `.cpp` file
- **C ABI:** no STL in the public API; suitable for FFI/cross-language use

```c
extern "C" void preprocess(
    const float* input,
    float* output,
    int N
);
```

- **Guarantees:** deterministic floating-point behavior; no dynamic allocation in the public API (v1 may allow it internally).

## Architecture

```
python_frontend/
    decorator.py
    ast_parser.py

core_ir/
    nodes.py
    type_system.py
    shape_inference.py
    serializer.py
    interpreter.py

backends/
    cpp_codegen.py
    runtime_templates/

tests/
    golden/
```
