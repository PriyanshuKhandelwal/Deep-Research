# OpenAI Agents SDK - Agent() Parameters Guide

This is a comprehensive list of all parameters you can pass to the `Agent()` constructor in the OpenAI Agents Python SDK.

## Core Parameters

### `name` (str, required)
- The name/identifier for the agent
- Used for identification in logs and tracing

### `instructions` (str or callable)
- Also known as a developer message or system prompt
- Can be a static string or a dynamic function that receives context and agent
- For dynamic instructions: `def dynamic_instructions(context: RunContextWrapper[UserContext], agent: Agent[UserContext]) -> str`

## Model Configuration

### `model` (str or Model instance)
- Which LLM to use
- Can be a string like `"o3-mini"`, `"gpt-4o"`, etc.
- Or a Model instance like `OpenAIChatCompletionsModel()`

### `model_settings` (ModelSettings)
- Optional model configuration parameters such as temperature
- Configure temperature, top_p, max_tokens, tool_choice, etc.
- Example: `ModelSettings(temperature=0.1, extra_args={"service_tier": "flex"})`

## Tool and Capability Configuration

### `tools` (list)
- Tools that the agent can use to achieve its tasks
- Can include function tools, hosted tools (WebSearchTool, FileSearchTool), etc.

### `handoffs` (list)
- Sub-agents that the agent can delegate to
- List of other Agent instances for delegation/routing

### `handoff_description` (str)
- Provide additional context for determining handoff routing
- Description used when this agent is a handoff target

## Output Configuration

### `output_type` (type)
- If you want the agent to produce a particular type of output
- Enables structured outputs using Pydantic models, dataclasses, TypedDict, etc.
- When you pass an output_type, that tells the model to use structured outputs instead of regular plain text responses

## Advanced Features

### `hooks` (AgentHooks)
- You can hook into the agent lifecycle with the hooks property
- Subclass AgentHooks to observe agent lifecycle events

### `input_guardrails` (list)
- Guardrails allow you to run checks/validations on user input
- List of input validation functions

### `output_guardrails` (list)
- Similar to input guardrails but for output validation

## Behavior Configuration

### `tool_use_behavior` (str)
- If you want the Agent to completely stop after a tool call (rather than continuing with auto mode), you can set `Agent.tool_use_behavior="stop_on_first_tool"`

### `reset_tool_choice` (bool)
- This behavior is configurable via agent.reset_tool_choice
- Controls whether tool_choice resets to "auto" after tool calls

## MCP Integration (if using MCP extension)

### `mcp_servers` (list) - *Available with MCP extension*
- Specify which MCP servers to use
- List of MCP server names defined in configuration

## Example Usage

```python
from agents import Agent, ModelSettings, function_tool
from pydantic import BaseModel

class OutputFormat(BaseModel):
    result: str
    confidence: float

@function_tool
def search_tool(query: str) -> str:
    return f"Results for {query}"

agent = Agent(
    name="Research Assistant",
    instructions="You are a helpful research assistant",
    model="gpt-4o",
    model_settings=ModelSettings(temperature=0.1),
    tools=[search_tool],
    output_type=OutputFormat,
    handoff_description="Specialist for research tasks"
)
```

## Quick Reference Table

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | str | ✅ | Agent identifier |
| `instructions` | str/callable | ✅ | System prompt/instructions |
| `model` | str/Model | ❌ | LLM model to use |
| `model_settings` | ModelSettings | ❌ | Model configuration |
| `tools` | list | ❌ | Available tools |
| `handoffs` | list | ❌ | Sub-agents for delegation |
| `handoff_description` | str | ❌ | Description for routing |
| `output_type` | type | ❌ | Structured output format |
| `hooks` | AgentHooks | ❌ | Lifecycle event hooks |
| `input_guardrails` | list | ❌ | Input validation |
| `output_guardrails` | list | ❌ | Output validation |
| `tool_use_behavior` | str | ❌ | Tool usage behavior |
| `reset_tool_choice` | bool | ❌ | Tool choice reset behavior |
| `mcp_servers` | list | ❌ | MCP servers (extension) |

## Notes

- Parameters marked with ✅ are required
- Parameters marked with ❌ are optional
- The `mcp_servers` parameter is only available when using the MCP extension package
- Dynamic instructions and guardrails support both sync and async functions