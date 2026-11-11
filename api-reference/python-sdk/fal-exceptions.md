> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../../documentation/quickstart.md) and the [Model APIs overview](../../documentation/model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../../documentation/serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](../../documentation/development/getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](../../documentation/development/getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](../../documentation/development/app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](../../documentation/development/endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../../documentation/deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../../documentation/deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../../documentation/serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../../documentation/deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../../documentation/serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](../../documentation/development/migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# fal.exceptions

> API reference for fal.exceptions

```python theme={null}
from fal.exceptions import (
    FalServerlessException,
    AppException,
    FieldException,
    RequestCancelledException,
    FileTooLargeError,
    AppFileUploadException,
    GPUException,
    GPUOutOfMemoryException,
    CUDAOutOfMemoryException,
    UnauthenticatedException,
    catch_gpu_exceptions,
)
```

## Classes

### FalServerlessException

```python theme={null}
class fal.exceptions.FalServerlessException
```

Base exception type for fal Serverless related flows and APIs.

> **Inherits from:** `Exception`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `args` | - | - | - |
> | `kwargs` | - | - | - |

### AppException

```python theme={null}
class fal.exceptions.AppException
```

Base exception class for application-specific errors.

> **Inherits from:** `FalServerlessException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | - | A descriptive message explaining the error. |
> | `status_code` | `int` | - | The HTTP status code associated with the error. |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | - | A descriptive message explaining the error. |
> | `status_code` | `int` | - | The HTTP status code associated with the error. |

### FieldException

```python theme={null}
class fal.exceptions.FieldException
```

Exception raised for errors related to specific fields.

> **Inherits from:** `FalServerlessException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `field` | `str` | - | The field that caused the error. |
> | `message` | `str` | - | A descriptive message explaining the error. |
> | `status_code` | `int` | `422` | The HTTP status code associated with the error. Defaults to 422 |
> | `type` | `str` | `'value_error'` | The type of error. Defaults to "value\_error" |
> | `billable_units` | `int \| float \| str \| NoneType` | `0` | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `field` | `str` | - | The field that caused the error. |
> | `message` | `str` | - | A descriptive message explaining the error. |
> | `status_code` | `int` | `422` | The HTTP status code associated with the error. Defaults to 422 |
> | `type` | `str` | `'value_error'` | The type of error. Defaults to "value\_error" |
> | `billable_units` | `int \| float \| str \| None` | `0` | - |

> [accordion] Methods
>
> #### to\_pydantic\_format
>
> ```python theme={null}
> def to_pydantic_format(self) -> 'dict[str, list[dict]]'
> ```
>
> **Returns:** `dict[str, list[dict]]`

### RequestCancelledException

```python theme={null}
class fal.exceptions.RequestCancelledException
```

Exception raised when the request is cancelled by the client.

> **Inherits from:** `FalServerlessException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'Request cancelled by the client.'` | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'Request cancelled by the client.'` | - |

### FileTooLargeError

```python theme={null}
class fal.exceptions.FileTooLargeError
```

Exception raised when the file is too large.

> **Inherits from:** `FalServerlessException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'File is too large.'` | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'File is too large.'` | - |

### AppFileUploadException

```python theme={null}
class fal.exceptions.AppFileUploadException
```

Raised when file upload fails

> **Inherits from:** `FalServerlessException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | - | - |
> | `relative_path` | `str` | - | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | - | - |
> | `relative_path` | `str` | - | - |

### GPUException

```python theme={null}
class fal.exceptions.GPUException
```

Base exception for GPU-related errors.

> **Inherits from:** `AppException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'GPU error'` | - |
> | `status_code` | `int` | `503` | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'GPU error'` | - |
> | `status_code` | `int` | `503` | - |

### GPUOutOfMemoryException

```python theme={null}
class fal.exceptions.GPUOutOfMemoryException
```

Exception raised when a GPU operation runs out of memory.

> **Inherits from:** `GPUException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'GPU error'` | - |
> | `status_code` | `int` | `503` | - |

### CUDAOutOfMemoryException

```python theme={null}
class fal.exceptions.CUDAOutOfMemoryException
```

Exception raised when a CUDA operation runs out of memory.

> **Inherits from:** `GPUOutOfMemoryException`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'CUDA error: out of memory'` | - |
> | `status_code` | `int` | `503` | - |

> [accordion] Class Variables
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `message` | `str` | `'CUDA error: out of memory'` | - |

### UnauthenticatedException

```python theme={null}
class fal.exceptions.UnauthenticatedException
```

Base exception type for fal Serverless related flows and APIs.

> **Inherits from:** `FalServerlessException`

### catch\_gpu\_exceptions

```python theme={null}
class fal.exceptions.catch_gpu_exceptions
```

Catch GPU/CUDA exceptions and convert them to HTTP 503 responses. Works as both a context manager and a decorator. Any caught GPU
exception (CUDA OOM, cuDNN errors, NVML failures, etc.) is
re-raised as a GPU exception with HTTP status 503.

**Example:**

```python theme={null}
from fal.exceptions import catch_gpu_exceptions

with catch_gpu_exceptions():
    run_inference()

@catch_gpu_exceptions()
def run_inference():
    ...
```

> **Inherits from:** `ContextDecorator`

> [accordion] Constructor Parameters
>
> | Name | Type | Default | Description |
> | :- | :- | :- | :- |
> | `args` | - | - | - |
> | `kwargs` | - | - | - |
