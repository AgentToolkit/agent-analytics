# Agent Analytics Common

**Shared data models and interfaces for the Agent Analytics platform**

## Overview

The `agent-analytics-common` package provides standardized Pydantic-based data models and type definitions used across both the Agent Analytics SDK and backend platform. These models ensure consistency in how agent behavior, performance, and semantic information is represented throughout the observability pipeline.

This package serves as the foundation for the Agent Analytics ecosystem, defining the core interfaces for tasks, actions, metrics, issues, annotations, and other analytical artifacts.

---

## Installation

The common package is automatically installed as a dependency when you install either the SDK or backend:

```bash
# Installed automatically with SDK
pip install "git+https://github.com/AgentToolkit/agent-analytics.git@main#subdirectory=sdk"

# Or installed automatically with backend
cd backend && npm run setup
```

---

## Data Models

All models are based on [Pydantic](https://docs.pydantic.dev/) for validation and serialization.

### Core Models

#### **Task** (`interfaces/task.py`)
Represents a unit of work performed by an agent.

**Key Classes:**
- `Task` - Base task model with timing, status, and hierarchy
- `TaskKind` - Classification of task intent (planning, retrieval, reasoning, action, etc.)
- `TaskStatus` - Execution status (success, failure, timeout, cancelled, unknown)
- `TaskTag` - Additional semantic tags (llm_call, tool_call, complex, manual, etc.)

**Use Cases:**
- Tracking hierarchical task decomposition
- Measuring task execution time and status
- Analyzing agent decision-making patterns
- Building task flow visualizations

---

#### **Action** (`interfaces/action.py`)
Represents a concrete operation or mechanism by which work is performed.

**Key Classes:**
- `Action` - Base action model with code, inputs, and outputs
- `ActionKind` - Type of action mechanism (tool, llm, ml, vector_db, workflow, guardrail, human, other)
- `ActionCode` - Code metadata (language, schema, implementation body)
- `WorkflowNodeType` - Workflow node types (start, end, invoke, select, fork, join)
- `CommandType` - Action lifecycle commands (load, invoke, update, unload)

**Use Cases:**
- Tracking tool/API invocations
- Monitoring LLM calls and prompts
- Analyzing workflow execution paths
- Code versioning and provenance

---

#### **Metric** (`interfaces/metric.py`)
Quantitative measurements and aggregations about agent performance.

**Key Classes:**
- `Metric` - Base metric model with value, category, and type
- `MetricCategory` - Metric classification (performance, quality, cost, security, human_in_the_loop)
- `MetricType` - Data type (numeric, distribution, string, time_series)

**Use Cases:**
- Performance monitoring (latency, throughput)
- Quality assessment (accuracy, hallucination rate)
- Cost tracking (token usage, API calls)
- Security metrics (guardrail triggers)

---

#### **Issue** (`interfaces/issues.py`)
Problems, errors, or anomalies detected during agent execution.

**Key Classes:**
- `Issue` - Base issue model with level, effect, and confidence
- `IssueLevel` - Severity levels (critical, error, warning, info, debug)

**Properties:**
- `timestamp` - When the issue was detected
- `effect` - Impact on the corresponding element
- `level` - Severity classification
- `confidence` - Detection confidence (0-1 scale)

**Use Cases:**
- Error detection and tracking
- Anomaly monitoring
- Debugging failed executions
- Quality assurance

---

#### **Annotation** (`interfaces/annotations.py`)
Semantic metadata describing data characteristics and context.

**Key Classes:**
- `DataAnnotation` - Annotates input/output data with semantic meaning
- `DataAnnotation.Type` - Extensive enum of annotation types:

  **Structural Classifications:**
  - Raw text, structured data, code snippets, multimodal data

  **Memory & Persistence:**
  - Short-term memory, long-term memory, RAG, static/dynamic data

  **Semantic & Functional:**
  - Hints, references, factual responses, analytical insights
  - Creative generation, code generation, decision support
  - Thought processes, explanations, summaries

  **Context & Provenance:**
  - User input, system prompt, tool output, retrieved context
  - Source attribution, citations

**Use Cases:**
- Enriching traces with semantic context
- Tracking agent "thinking" and reasoning
- Documenting data provenance
- Supporting explainability and interpretability

---

#### **Recommendation** (`interfaces/recommendations.py`)
Actionable suggestions for improving agent performance or behavior.

**Key Classes:**
- `Recommendation` - Base recommendation model with impact level and effects
- `RecommendationLevel` - Impact levels (critical, major, moderate, minor)

**Properties:**
- `timestamp` - When the recommendation was generated
- `effect` - Expected benefits of implementing the recommendation
- `level` - Importance/priority classification

**Use Cases:**
- Performance optimization suggestions
- Configuration tuning recommendations
- Best practice guidance
- Automated remediation suggestions

---

### Supporting Models

#### **Element** (`interfaces/elements.py`)
Base class for all observable elements with common properties:
- `element_id` - Unique identifier
- `name` - Human-readable name
- `description` - Detailed description
- `attributes` - Key-value metadata

#### **RelatableElement** (`interfaces/relatable_element.py`)
Extends Element with relationship tracking:
- `related_to_ids` - Links to other elements (tasks, actions, etc.)

#### **Graph** (`interfaces/graph.py`)
Workflow and execution flow representations:
- `Graph.Node` - Nodes in execution graphs
- `Graph.Edge` - Connections between nodes

#### **Event** (`interfaces/events.py`)
Discrete occurrences during agent execution.

#### **Resource** (`interfaces/resources.py`)
External resources accessed by agents (databases, APIs, files).

#### **Runnable** (`interfaces/runnable.py`)
Executable components within agent workflows.

#### **IUnit** (`interfaces/iunits.py`)
Base interface for instrumentation units.

---

## Package Structure

```
common/
├── src/
│   └── agent_analytics_common/
│       ├── __init__.py
│       └── interfaces/              # Data model definitions
│           ├── __init__.py
│           ├── action.py            # Action models and enums
│           ├── annotations.py       # Data annotation types
│           ├── elements.py          # Base element classes
│           ├── events.py            # Event models
│           ├── graph.py             # Graph structures
│           ├── issues.py            # Issue tracking models
│           ├── iunits.py            # Instrumentation units
│           ├── metric.py            # Metric definitions
│           ├── recommendations.py   # Recommendation models
│           ├── relatable_element.py # Relational base class
│           ├── resources.py         # Resource models
│           ├── runnable.py          # Runnable interface
│           └── task.py              # Task models and enums
├── setup.py                         # Package configuration
├── requirements.txt                 # Dependencies (pydantic)
└── README.md                        # This file
```

---

## Usage Examples

### Importing Models

```python
from agent_analytics_common.interfaces.task import Task, TaskKind, TaskStatus
from agent_analytics_common.interfaces.action import Action, ActionKind
from agent_analytics_common.interfaces.annotations import DataAnnotation
from agent_analytics_common.interfaces.issues import Issue, IssueLevel
from agent_analytics_common.interfaces.metric import Metric, MetricCategory
```

### Creating a Task

```python
task = Task(
    name="web_search",
    description="Search for information using web API",
    kind=TaskKind.RETRIEVAL,
    status=TaskStatus.SUCCESS,
    tags=[TaskTag.TOOL_CALL]
)
```

### Recording an Issue

```python
issue = Issue(
    name="timeout",
    description="Search API timeout exceeded",
    level=IssueLevel.WARNING,
    effect=["Task delayed by 5 seconds"],
    confidence=0.95
)
```

### Adding Annotations

```python
annotation = DataAnnotation(
    name="agent_thought",
    annotation_type=DataAnnotation.Type.THOUGHT,
    annotation_title="Reasoning Step",
    annotation_content="I need to search for recent data before answering"
)
```

---

## Dependencies

- **pydantic** >= 2.0.0, < 3.0.0 - Data validation and serialization

---

## Design Principles

1. **Pydantic-First**: All models use Pydantic for validation, serialization, and type safety
2. **OpenTelemetry Alignment**: Models align with OTEL semantic conventions where applicable
3. **Extensibility**: Enums and base classes support custom extensions
4. **Immutability**: Models are designed to be created once and remain unchanged
5. **Serialization**: All models support JSON serialization for storage and transmission

---

## Contributing

When adding new models or extending existing ones:

1. Use Pydantic `BaseModel` as the foundation
2. Include comprehensive docstrings
3. Define clear enum types for categorical fields
4. Add JSON serialization methods (`from_dict`, `from_json`, `to_dict`, `to_json`)
5. Maintain backward compatibility

---

## Version

Current version: **0.1.6**

---

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](../LICENSE) file for details.
