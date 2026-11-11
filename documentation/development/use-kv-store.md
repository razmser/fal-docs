> ## Documentation Index
> Fetch the complete documentation index at: https://fal.ai/docs/llms.txt
> Use this file to discover all available pages before exploring further.

> ## Agent Instructions
> fal has two developer products. Model APIs run hosted models through an API key. fal Serverless deploys your own Python apps and models on fal GPUs.
> To call a hosted model, start with the [Quick Start](../quickstart.md) and the [Model APIs overview](../model-apis/overview.md).
> To deploy your own model with fal Serverless, start with these pages:
> - [Introduction to Serverless](../serverless.md): What fal Serverless is and the three ways to deploy on it.
> - [Installation & Setup](getting-started/installation.md): Install the fal CLI with `pip install fal` and authenticate.
> - [Quick Start](getting-started/quick-start.md): Build a Hello World app, test it with `fal run`, and ship it with `fal deploy`.
> - [App Lifecycle](app-lifecycle.md): How a `fal.App` goes from code to running runners.
> - [Define Your Endpoints](endpoints-overview.md): Structure the API endpoints that your app exposes.
> - [Deploy to Production](../deployment/deploy-to-production.md): Persistent URLs, authentication modes, and automatic scaling.
> - [Machine Types](../deployment/machine-types.md): Available GPU and CPU machine types and how to choose one.
> - [Pricing](../serverless/pricing.md): Per-second billing and the runner states that are billed.
> - [Scaling Parameter Reference](../deployment/scale-your-application.md): Parameters that control runners, concurrency, and scale to zero.
> - [Optimizing Cold Starts](../serverless/optimizations/optimize-cold-starts.md): Causes of cold starts and ways to make them shorter.
> - [Examples](../../examples.md): Complete Serverless apps for image, video, audio, 3D, realtime, and multi-GPU workloads.
> - [Migrating to fal](migrating-to-fal.md): Guides to move an existing Docker server or an app from another platform to fal.
> fal Serverless deploys need access that the fal team approves for each account. Request access at https://fal.ai/dashboard/serverless-get-started.

# Use KV Store

> KVStore is a simple key-value storage for sharing state across serverless runners with zero setup required.

KVStore provides a way to share small string values across all your serverless runners. It supports three operations: `get`, `set`, and `delete`. Use it for caching computation results, sharing configuration between runners, or storing small pieces of state that multiple runners need to read. It requires no setup and uses automatic authentication with your fal credentials.

Unlike [persistent storage](use-persistent-storage.md) on `/data` (a distributed filesystem for large files like model weights), KVStore is a remote key-value API accessed over HTTP. It is best suited for small, frequently-read values where you need cross-runner consistency. It does not support listing keys or atomic operations, so it is not appropriate for counters, locks, or anything requiring transactional guarantees.

## API Reference

### Initialize KVStore

```python theme={null}
from fal.toolkit.kv import KVStore

kv = KVStore("myapp")  # "myapp" is your namespace
```

Each namespace is isolated - different `db_name` values create separate key-value stores.

### Methods

**`get(key: str) -> Optional[str]`**

Retrieve a value from the store. Returns `None` if the key doesn't exist.

```python theme={null}
value = kv.get("my-key")
if value is None:
    print("Key not found")
```

**`set(key: str, value: str, ttl: Optional[int] = None) -> None`**

Store a string value. The value must be a string - use `json.dumps()` for objects.

Values are limited to **1.9 MB** each. Attempting to store a larger value will fail. Each store (namespace) has a total capacity of **10 GB** across all keys.

`ttl` is the time-to-live in seconds. It must be an integer between `60` (1 minute) and `2592000` (30 days). When omitted, entries expire after 30 days.

```python theme={null}
import json

# Store a string (expires after the default 30 days)
kv.set("my-key", "my-value")

# Store an object as JSON
kv.set("config", json.dumps({"enabled": True, "limit": 100}))

# Expire after one hour
kv.set("session", session_token, ttl=3600)
```

**`delete(key: str) -> None`**

Delete a value from the store. The operation is idempotent: it succeeds whether or not the key exists, and returns no signal about prior existence.

```python theme={null}
kv.delete("my-key")
```

## Basic Example

```python theme={null}
import fal
import json
from fal.toolkit.kv import KVStore

class MyApp(fal.App):
    def setup(self):
        self.kv = KVStore("myapp")
    
    @fal.endpoint("/")
    def process(self, input: Input):
        # Check cache
        cache_key = f"result-{input.id}"
        cached = self.kv.get(cache_key)
        
        if cached:
            return json.loads(cached)
        
        # Compute and cache result
        result = self.expensive_operation(input)
        self.kv.set(cache_key, json.dumps(result))
        
        return result
```

## Common Use Cases

### Caching API Responses

Cache external API calls to reduce latency and costs:

```python theme={null}
class MyApp(fal.App):
    def setup(self):
        self.kv = KVStore("api-cache")
    
    @fal.endpoint("/")
    def process(self, input: Input):
        cache_key = f"weather:{input.city}"
        
        # Check cache
        cached = self.kv.get(cache_key)
        if cached:
            return json.loads(cached)
        
        # Fetch from external API
        weather = requests.get(f"https://api.weather.com/{input.city}").json()
        self.kv.set(cache_key, json.dumps(weather))
        
        return weather
```

### Caching Computation Results

Store results of expensive operations to avoid recomputation:

```python theme={null}
class MyApp(fal.App):
    def setup(self):
        self.kv = KVStore("compute-cache")
    
    @fal.endpoint("/")
    def process(self, input: Input):
        cache_key = f"result:{input.video_id}"
        
        cached = self.kv.get(cache_key)
        if cached:
            return json.loads(cached)
        
        # Expensive video processing
        result = self.process_video(input.video_url)
        self.kv.set(cache_key, json.dumps(result))
        
        return result
```

## Limitations

KVStore is deliberately simple. It supports `get`, `set`, and `delete` on string values, with an optional per-key TTL (1 minute to 30 days). There is no key listing/scanning and no atomic operations (no increment, no compare-and-swap). Every entry expires - values cannot be kept indefinitely without being rewritten.

Each value is limited to 1.9 MB, and each store (namespace) has a total capacity of 10 GB across all keys.

If multiple runners write to the same key simultaneously, the last write wins with no conflict detection. This makes KVStore unsuitable for counters, locks, rate limiting, or anything requiring atomicity. For those use cases, use an external database like Redis or PostgreSQL.

For large files (model weights, datasets), use the [`/data` volume](use-persistent-storage.md) instead.
