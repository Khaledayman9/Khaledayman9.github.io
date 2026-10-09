---
title: "llm-refract: Record, Understand, and Improve Your AI Applications"
date: 2026-09-20
updatedDate: 2026-10-09
excerpt: "See every step your AI takes, find expensive or slow calls, try another model, and catch regressions before shipping. A complete tour of llm-refract's provider integrations, Inspector, replay, evaluation, and local-to-production workflows."
coverImage: https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Overview.PNG
coverImageAlt: "Current llm-refract Inspector showing a returns assistant's cost, latency, token usage, comparison controls, and retrieval-to-generation graph"
logo: "https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/llm-refract-logo.svg"
logoAlt: "llm-refract green diamond logo"
references:
  - label: "GitHub — llm-refract source, installation, and examples"
    url: "https://github.com/khaleddeissa/llm-refract"
  - label: "Documentation — all interfaces and usage guides"
    url: "https://github.com/khaleddeissa/llm-refract/blob/main/docs/README.md"
  - label: "Providers — supported SDK methods and adapters"
    url: "https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/providers.md"
  - label: "Advanced integrations — local inference, Realtime, and LangGraph"
    url: "https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/advanced-integrations.md"
  - label: "Production — deployment, delivery, and recovery"
    url: "https://github.com/khaleddeissa/llm-refract/blob/main/docs/production.md"
  - label: "PyPI — llm-refract Python SDK"
    url: "https://pypi.org/project/llm-refract/"
  - label: "npm — @llm-refract/sdk"
    url: "https://www.npmjs.com/package/@llm-refract/sdk"
  - label: "GHCR — server, CLI, and Inspector container"
    url: "https://github.com/khaleddeissa/llm-refract/pkgs/container/llm-refract"
  - label: "GitHub Action — regression setup and current availability"
    url: "https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/ci.md"
  - label: "GitHub Marketplace — supplied Action link (see availability note above)"
    url: "https://github.com/marketplace/actions/refract-execution-regression"
---

Your AI assistant gives the wrong answer. Was the document wrong? Did a tool fail? Did the model ignore the evidence? And why did this request take twice as long as yesterday's?

I built [**llm-refract**](https://github.com/khaleddeissa/llm-refract) to make those questions answerable. It records what your AI application does, shows how the steps connect, and lets you compare what happens when you change the model, prompt, or code.

Think of it as a flight recorder and an experiment workspace for AI applications. One recording can follow a simple model call, a RAG pipeline that retrieves documents before answering, a tool-using agent, or a workflow involving several agents. You can inspect the evidence, create a new branch, and measure whether a change actually helped.

This updated tour covers the repository's **v0.1.5 source feature set**. Published Python, npm, and container versions may lag the checkout; the [development guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/development.md) explains how to build the current source.

## Watch the workflow

<figure>
  <video controls playsinline preload="metadata" poster="https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Overview.PNG" width="1600" height="1000" aria-label="llm-refract Inspector demo" aria-describedby="refract-demo-caption">
    <source src="https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Demo.webm" type="video/webm">
    Your browser cannot play this video. <a href="https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Demo.webm">Download the Inspector demo</a>.
  </video>
  <figcaption id="refract-demo-caption">The repository's Inspector demo: inspect a run, compare a changed return policy, create a model branch, choose an embedding model, and browse telemetry. These captures use a running local service with deterministic demo data; the displayed measurements illustrate the workflow.</figcaption>
</figure>

<details>
<summary>Read the demo walkthrough</summary>
<ol>
  <li>Open the returns assistant's baseline. The overview shows recorded cost, tokens, latency, and a graph connecting policy retrieval to the answer.</li>
  <li>Select the answer to inspect its prompt, output, model attributes, and parent event.</li>
  <li>Compare the baseline with a candidate. The report identifies a return deadline that changed from 30 days to 14 days.</li>
  <li>Choose an approved model, authorize its calls, and create a separate branch while preserving the original recording.</li>
  <li>Enable a project embedding model and search the recordings by text.</li>
  <li>Open the telemetry panel to inspect a log record with its API key redacted.</li>
</ol>
</details>

## Follow the answer back to its source

Imagine a returns assistant. It retrieves the store's policy, checks an order, decides whether the order qualifies, and drafts a reply. llm-refract can capture the model calls, retrieval results, tool calls, decisions, state changes, checkpoints, handoffs, human steps, and failures along that path.

Each recorded event can include its input, output, status, timing, attributes, and relationship to an earlier event. That relationship lets you follow an answer back to the evidence it used. A model proposing a tool call and your application actually executing that tool are separate things you can record.

The **Inspector** brings this together in a browser. Select a run, follow its graph or keyboard-accessible timeline, and click a step to inspect the captured data. The overview highlights model and tool counts, token usage, estimated cost, latency, and the slowest or most expensive event.

![Inspector showing a recorded retrieval connected to a generation, with the answer's input, output, measurements, and replay policy](https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Layout_1.PNG)

_The current Inspector, captured from the project's local demo. The graph shows recorded parent relationships; selecting a step reveals its evidence. Click any screenshot to enlarge it._

## Connect the providers and frameworks you already use

An **adapter** is a small wrapper around an existing client. Enable it, call your model normally, and it records the supported calls inside an active run. Your application keeps control of credentials, endpoints, model selection, and retries.

Supported adapters capture inputs, outputs, reported usage, errors, and streaming measurements where the provider exposes them. Sync and async workflows are supported. Consume a stream inside the recording context so its final output and usage can be captured.

| Integration                            | What you can record                                                                                                                                                              |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **OpenAI**                             | Responses and Chat Completions, including streams, in Python and Node.                                                                                                           |
| **Anthropic**                          | Messages and streaming responses in both SDKs.                                                                                                                                   |
| **Azure OpenAI and Microsoft Foundry** | Configured Azure clients and compatible `/openai/v1/` deployments, using the application's authentication.                                                                       |
| **Google Gemini and Vertex AI**        | Google Gen AI generation and streams; Python also has a legacy Vertex adapter.                                                                                                   |
| **Amazon Bedrock**                     | Converse and ConverseStream, plus native InvokeModel APIs through dedicated adapters. Python supports configured Boto/async clients; Node supports AWS SDK v3 clients.           |
| **Ollama and vLLM**                    | OpenAI-compatible endpoints where supported; Ollama also has a library adapter.                                                                                                  |
| **Hugging Face and llama.cpp**         | Library adapters for supported inference-client methods. A Python Transformers example records inference using existing local weights.                                           |
| **Private or custom models**           | Custom adapters select the methods to wrap and translate requests, responses, and stream chunks into recordable data. Manual events cover other APIs.                            |
| **LiteLLM**                            | Python completion and Responses methods, including async and streaming calls, on the module or a configured Router. Node can use a proxy through its compatible OpenAI endpoint. |
| **Realtime sessions**                  | Python and Node adapters record response lifecycles, text, and reported usage. Raw binary audio is excluded.                                                                     |

Framework integrations add the surrounding workflow. **LangChain callbacks** record chains, model calls, retrieval, and tools; Node also offers model wrapping. Supported inline callback/provider combinations avoid counting the same generation twice. Python's **LangGraph runtime** can record a checkpoint and resume through the application's compiled graph and checkpoint store. Node LangChain/LangGraph callbacks capture runnable events; the explicit checkpoint-continuation API is Python.

You can also export completed recordings to **Langfuse** or other **OpenTelemetry** backends. That connects the execution evidence to an existing observability stack.

Provider-neutral recording means these integrations share one event format. The [provider guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/providers.md) and [advanced integration guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/advanced-integrations.md) document the covered methods. Refract observes your inference setup; local model servers and weights remain application dependencies.

## Start with one recording

Install the Python SDK with `pip install llm-refract` on Python 3.11 or newer. This small example needs no model account or server:

```python
import refract

with refract.run("returns-assistant", path="returns.rfr"):
    policy = refract.event(
        type="retrieval",
        name="Find return policy",
        output={"return_window_days": 30},
    )
    refract.event(
        type="generation",
        name="Draft answer",
        parent_id=policy,
        output={"text": "You can return your order within 30 days."},
        attributes={"provider": "example", "model": "demo"},
    )
```

These are demonstration values. In an application, record the actual retrieval output and use an adapter around your model client to capture its response automatically. For example, after installing the `openai` extra, `handle = refract.instrument_openai()` enables supported Python OpenAI capture; restore it with `handle.uninstrument()` at shutdown. Custom adapters let you choose exactly which fields become evidence.

For Node, install `npm install @llm-refract/sdk`:

```typescript
import { refract } from "@llm-refract/sdk";

await refract.run(
  "order-assistant",
  async () => {
    refract.event({
      type: "tool.call",
      name: "check_delivery",
      output: { status: "delivered" },
    });
  },
  { path: "order.rfr" },
);
```

Both SDKs isolate concurrent runs and support explicit events and nested recording workflows. Python also provides sync/async decorators. Add `endpoint="http://localhost:8000"` in Python, or `endpoint: "http://localhost:8000"` in the Node options, to submit to a running workspace. Background exporters provide a separate path for queued delivery in production. See the [Python](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/python.md) and [TypeScript](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/typescript.md) guides.

## Carry the execution with you

A `.rfr` file is a portable execution recording. New files are readable UTF-8 text: a format/checksum header followed by formatted JSON. You can open one in an editor, attach it to an investigation, or use it as a reviewed test baseline. The Rust reader also supports older ZIP recordings.

With the Rust CLI installed from the source checkout using `cargo install --path crates/refract-cli`, you can work entirely offline:

```bash
refract validate returns.rfr
refract inspect returns.rfr
refract replay returns.rfr
refract metrics returns.rfr
refract unpack returns.rfr -o returns.json
```

Validation checks the recording, inspection shows its events, and unpacking gives you ordinary execution JSON. Packing provides the reverse path. The checksum detects corruption; exported files still need the same care as any file containing application data. Details are in the [artifact guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/artifacts.md).

## Replay what happened, then try something new

Refract offers three related operations with different purposes:

| Operation            | What happens                                                                                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Recorded replay**  | Returns saved outputs under the recorded policies, without contacting models or executing tools. Useful for inspecting an existing run without repeating its side effects. |
| **Prefix fork**      | Copies everything before a selected event into a new run and records where it came from. This gives an experiment a shared starting point.                                 |
| **Executable rerun** | Runs the selected step and subsequent steps using configured model profiles or trusted application handlers, producing fresh evidence in a new branch.                     |

In the Inspector, **Rerun with a model** lets you select an operator-approved model profile, authorize its calls, and create a branch. The original recording stays intact. You can compare the new answer and measurements directly with the baseline.

![Inspector with the model-rerun panel expanded, an approved local model selected, and explicit authorization for provider calls](https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Layout_3.PNG)

_A model experiment starts at the selected event. The demo uses a local profile; deployed profiles can point to approved hosted or local models._

Server profiles support OpenAI Chat/Responses, Anthropic, Gemini, Ollama, and custom HTTP integrations. Compatible gateways can use the matching OpenAI protocol. Python's built-in provider executors additionally support application clients such as Bedrock Converse and LiteLLM. CLI executors and SDK handlers let your application rerun its own tools or implementation.

For a multi-step continuation, handlers or explicit input bindings tell the next step how to use earlier results. LangGraph continuation uses the application's real checkpoint store and compatible graph code. A recording carries evidence and lineage; it cannot restore arbitrary process memory.

Live calls require explicit authorization. Blocked events stay blocked, and steps that need individual approval retain that requirement. Server model reruns require explicit reuse of non-model steps; executing those application tools belongs in trusted SDK/CLI handlers. The [rerun guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/rerun.md) covers each route.

## Compare answers, speed, and cost together

Suppose you replace the model in the returns assistant. It replies faster and uses fewer tokens, but changes the return window from **30 days to 14 days**. That is a cheaper wrong answer. The comparison needs to show both the behavioral change and the measurements.

**Strict diff** compares ordered recorded events, including inputs, outputs, attributes, status, and parent relationships, while ignoring generated identifiers and timing. **Semantic comparison** adds output grading so you can assess changes in wording while retaining structural checks. Provider/model labels and measurement attributes are handled separately in semantic mode, making model experiments easier to compare.

![Inspector comparing baseline and candidate metrics and reporting that the return window changed from 30 days to 14 days](https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Layout_2.PNG)

_The demo comparison identifies the changed deadline. Its local mock grader demonstrates the reporting flow; it is not a model-quality benchmark._

The built-in offline grader uses word overlap, limited synonym handling, and number/negation checks. For domain-specific judgments, use a custom CLI grader or an approved service model with a configured rubric. The Inspector, API, SDKs, and optional live MCP tools can use configured model grading. Invalid model judgments fail the comparison rather than silently passing it.

Measurements include input/output tokens, available cache usage, estimated cost, wall latency, event durations, failures, and **time to first token**—how long a streaming user waits before output starts. Coverage tells you which calls actually supplied measurements. Missing usage or pricing stays unknown.

Prices come from explicit configuration. Python can refresh an approved versioned price feed and reconcile estimates with invoice rows you map to recorded events. Node accepts configured pricing mappings. Estimates remain attached to their original recordings; invoices remain the billing authority. See [metrics](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/metrics.md) and [pricing](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/pricing.md).

You can turn expectations into a check:

```bash
refract diff baseline.rfr candidate.rfr --semantic --threshold 0.85 \
  --max-cost-increase-percent 10 \
  --max-latency-increase-percent 20 \
  --max-token-increase-percent 5
```

Here the candidate must pass the output comparison and stay within the configured cost, latency, and token increases. A requested budget fails when its required measurements are incomplete.

## Make good runs repeatable tests

A dataset groups named cases—such as a normal return, an expired return, and a failed tool lookup—with a baseline and a freshly generated candidate for each. Dataset-wide options and per-case overrides control grading and budgets.

```bash
refract eval tests/executions --report evaluation-report.json
```

The report explains each case, its measurements, and its pass/fail result. Custom graders can be used from the CLI; stored run pairs can also be evaluated through the API and MCP.

The included **GitHub Action** validates and compares a baseline with a fresh application recording, writes `refract-report.json`, and fails the step on regressions or invalid artifacts. Its `comparison-options` input enables semantic and budget settings in versions that include that feature. A CI job can also run `refract eval` for a whole dataset.

The essential sequence is: **run your changed application, capture its output, compare it with a reviewed baseline**. Evaluation reads those recordings; it does not run the application for you. The [evaluation guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/evaluation.md) and [CI guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/ci.md) provide manifests and workflow setup.

## Find the runs that matter

Once you have many recordings, search becomes part of debugging. Structured filters find failed runs, a particular model or tool, slow events, expensive recorded calls, or a time window. Local lexical similarity finds recordings that share words with a selected run.

**Semantic text search** uses an embedding model—a model that turns text into numbers so related content can be matched. Operators configure local or hosted embedding profiles; project administrators enable models, choose a default, and turn on automatic indexing. Users pick an enabled model in the Inspector without receiving its credentials.

![Inspector showing project embedding settings, automatic indexing, an enabled local model, and text search for a return policy](https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Search.PNG)

_Find related executions with a selected embedding model. The demo uses a small local lexical-vector fixture to demonstrate the connection._

Applications can also supply their own vectors. Search keeps models and tenant scopes separate and supports exact ranking or a faster approximate HNSW index for larger collections. Background indexing has retries and reindexing controls. These paths are available through the CLI, SDKs, API, and MCP; the [search guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/search.md) explains model setup and index behavior.

## Connect traces, logs, and metrics

OpenTelemetry support lets Refract fit into systems that already emit telemetry. Python and Node can convert recordings to and from OTLP JSON and export completed runs; Python can also import completed OpenTelemetry SDK spans. Langfuse exports include model, usage, cost, and event-type information where recorded.

The service accepts **traces, logs, and metrics over OTLP HTTP JSON, HTTP protobuf, and gRPC**. Distributed spans are assembled by trace ID within the authenticated scope. If a parent arrives later, the service creates an updated immutable snapshot of the graph.

![OpenTelemetry panel showing a log record whose api_key attribute has been replaced with REDACTED](https://raw.githubusercontent.com/khaleddeissa/llm-refract/40869d859cf98510a5f0e6a9b1f8ec8cc384fc6f/assets/Inspector_Telemetry.PNG)

_Inspect logs and metrics in the same workspace. This captured log shows secret-key redaction._

Logs retain severity and trace/span references; metrics retain exported data points and temporality. They are stored separately from replayable executions and can be queried through the Inspector, SDKs, API, and MCP. The [OpenTelemetry guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/otel.md) covers ingestion and export configuration.

## Choose the interface that fits your work

All of these workflows share a Rust engine and a common execution model. The SDKs capture data; the engine validates, stores, replays, and compares it. The UI and MCP use the same service API.

<pre class="mermaid">
flowchart TD
  App[Your AI application] --> SDK[Python / TypeScript / native events]
  SDK --> File[Portable .rfr recording]
  SDK --> API[Rust REST API]
  OTel[OpenTelemetry signals] --> API
  File --> CLI[Rust CLI and libraries]
  CLI --> Checks[Replay / rerun / diff / evaluation]
  API --> DB[(SQLite or PostgreSQL)]
  UI[Browser Inspector] --> API
  Agent[MCP and agent skills] --> API
  API --> Profiles[Configured generation and embedding models]
  Checks --> CI[Regression checks in CI]
</pre>

| Interface                | When to use it                                                                                                                                 |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Python SDK**           | Record Python applications, wrap providers, capture async work, deliver snapshots, and run supported continuations.                            |
| **TypeScript / npm SDK** | Instrument Node clients, record streams and concurrent work, read/write artifacts, and use service APIs.                                       |
| **Rust crates**          | Embed validation, storage, artifacts, metrics, replay executors, grading, and evaluation in a Rust program.                                    |
| **CLI**                  | Inspect files, validate, pack/unpack, replay, fork, rerun, diff, evaluate, search service data, or start the server.                           |
| **REST API**             | Build integrations for ingestion, runs, graphs, exports, search, metrics, comparison, model experiments, and administration.                   |
| **Docker + Inspector**   | Run the server and visual workspace together with persistent storage.                                                                          |
| **MCP**                  | Let an agent inspect execution evidence and use explicitly enabled write/live operations.                                                      |
| **Agent skills**         | Give an agent task-specific instructions for debugging, inspection, replay, forks, diffs, regression checks, search, exports, and diagnostics. |
| **GitHub Action**        | Make execution comparison part of a pull request or build.                                                                                     |
| **`.rfr` files**         | Move evidence between those interfaces, including offline environments.                                                                        |

For an agent, a useful task is: “Find the first difference between these runs and explain which step caused the changed answer.” MCP supplies inspection, graphs, first divergence, metrics, evaluation, search, telemetry, and recorded playback. Optional tools import runs or create prefix forks; separate live controls enable configured model grading and reruns. The server still enforces roles and scope. Setup is in the [MCP](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/mcp.md) and [skills](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/skills.md) guides.

## Work offline, locally, or as a shared production service

### Offline: a file and your own tools

Write `.rfr` files and inspect them with the CLI. Recording demonstration data and inspecting saved runs require no account, server, or model key. Actual inference uses whatever local or hosted setup your application already has. This mode works well for development, CI, and restricted networks.

### Local: a persistent browser workspace

The container bundles the Rust server, CLI, and React Inspector. Start a workspace with:

```bash
docker run --rm \
  -p 127.0.0.1:8000:8000 \
  -v refract-data:/data \
  ghcr.io/khaleddeissa/llm-refract:latest
```

Open `http://localhost:8000` and submit recordings using an SDK endpoint. SQLite stores the data in the named volume, which survives container removal. To build the current source feature set, clone the repository and run `docker compose up --build -d --wait` from its root. The [Docker guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/docker.md) covers both paths.

### Production: shared access and reliable capture

Production mode adds the controls a team needs around its evidence:

- **Scoped access:** reader, writer, and administrator roles tied to an organization, project, and environment. Managed API keys can expire, rotate, and be revoked; shared quotas work across replicas.
- **Company sign-in:** OIDC authentication and persistent browser SSO sessions, plus SCIM user/group provisioning so directory membership can control access and deactivation can revoke it.
- **Data protection:** key-based and configurable text/email redaction, AES-256-GCM payload encryption, keyring rotation, retention, and separate audit export/expiry. PostgreSQL can add row-level security through a restricted runtime role.
- **Reliable application capture:** bounded background queues, batching, retries, optional disk spools, and durable disk acceptance. Failure policies let you decide whether recording trouble should affect the application; Python also provides deterministic sampling.
- **External delivery:** a persistent outbox sends stored payloads to signed webhooks or S3-compatible storage. Python and Node receiver helpers verify signatures, deduplicate receipts, and apply the newest delivery version.
- **Operations:** readiness checks, audit records, delivery monitoring, database migrations, and documented backup, restore, upgrade, and shutdown procedures.

The supplied production Compose profile combines Refract, PostgreSQL, and Caddy for HTTPS. Production startup requires authentication, a valid encryption configuration, and confirmation that HTTPS terminates at the proxy. The backend stays on an internal network.

Delivery can retry, so its transport guarantee is **at least once**. Exported `.rfr` files and searchable database metadata also have different protection boundaries from encrypted payloads. Operators configure TLS, keys, egress, backups, and retention for their environment. The [production guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/production.md), [service controls](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/service-controls.md), and [recovery guide](https://github.com/khaleddeissa/llm-refract/blob/main/docs/usage/recovery.md) explain the deployment details.

## Try it on one real question

Start with a question your application already answers. Record the retrieval, tool work, and model response. Open the run and follow the answer back to its inputs. Then change one thing—a prompt, a model, a retrieval step—and capture the candidate.

Now you can ask concrete questions: Did the answer preserve the facts? Did the workflow change? Did it get faster? Did it use fewer tokens? Is the cost estimate complete? Can this example become a regression case?

That is what I wanted llm-refract to make practical: **understand an AI execution, experiment from that evidence, and keep the improvement measurable**.

The project is open source under Apache-2.0. Explore the [repository](https://github.com/khaleddeissa/llm-refract) and [runnable examples](https://github.com/khaleddeissa/llm-refract/blob/main/examples/README.md) to choose your first integration.
