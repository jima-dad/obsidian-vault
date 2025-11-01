---
title: "Building Effective AI Agents"
source: "https://www.anthropic.com/engineering/building-effective-agents"
author: "Anthropic"
published: 2025-05-14
created: 2025-05-14
updated: 2025-05-14
description: "Discover how Anthropic approaches the development of reliable AI agents. Learn about their research on agent capabilities, safety considerations, and technical framework for building trustworthy AI."
tags:
  - "AI"
  - "agents"
  - "Anthropic"
  - "LLM"
  - "workflow"
related_permanent:
  - "[[AIエージェントとリポジトリ知識連携の未来]]"
---

# Building Effective AI Agents

Over the past year, Anthropic has worked with dozens of teams building large language model (LLM) agents across industries. Consistently, the most successful implementations weren't using complex frameworks or specialized libraries. Instead, they were building with simple, composable patterns.

## What are agents?

"Agent" can be defined in several ways. Some customers define agents as fully autonomous systems that operate independently over extended periods, using various tools to accomplish complex tasks. Others use the term to describe more prescriptive implementations that follow predefined workflows. At Anthropic, they categorize all these variations as **agentic systems**, but draw an important architectural distinction between **workflows** and **agents**:

- **Workflows** are systems where LLMs and tools are orchestrated through predefined code paths.
- **Agents**, on the other hand, are systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks.

## When (and when not) to use agents

When building applications with LLMs, finding the simplest solution possible is recommended, and only increasing complexity when needed. This might mean not building agentic systems at all. Agentic systems often trade latency and cost for better task performance, and this tradeoff should be considered carefully.

When more complexity is warranted, workflows offer predictability and consistency for well-defined tasks, whereas agents are the better option when flexibility and model-driven decision-making are needed at scale. For many applications, however, optimizing single LLM calls with retrieval and in-context examples is usually enough.

## When and how to use frameworks

There are many frameworks that make agentic systems easier to implement, including:

- LangGraph from LangChain
- Amazon Bedrock's AI Agent framework
- Rivet, a drag and drop GUI LLM workflow builder
- Vellum, another GUI tool for building and testing complex workflows

These frameworks make it easy to get started but often create extra layers of abstraction that can obscure the underlying prompts and responses, making them harder to debug. They can also make it tempting to add complexity when a simpler setup would suffice.

Anthropic suggests that developers start by using LLM APIs directly, as many patterns can be implemented in a few lines of code. If using a framework, ensure you understand the underlying code.

## Building blocks, workflows, and agents

### Building block: The augmented LLM

The basic building block of agentic systems is an LLM enhanced with augmentations such as retrieval, tools, and memory. Current models can actively use these capabilities—generating their own search queries, selecting appropriate tools, and determining what information to retain.

### Workflow: Prompt chaining

Prompt chaining decomposes a task into a sequence of steps, where each LLM call processes the output of the previous one. You can add programmatic checks on any intermediate steps to ensure that the process is still on track.

**When to use this workflow:** Ideal for situations where the task can be easily and cleanly decomposed into fixed subtasks. The main goal is to trade off latency for higher accuracy, by making each LLM call an easier task.

**Examples:**

- Generating marketing copy, then translating it into a different language.
- Writing an outline of a document, checking that the outline meets certain criteria, then writing the document based on the outline.

### Workflow: Routing

Routing classifies an input and directs it to a specialized followup task. This workflow allows for separation of concerns, and building more specialized prompts.

**When to use this workflow:** Works well for complex tasks where there are distinct categories that are better handled separately, and where classification can be handled accurately.

**Examples:**

- Directing different types of customer service queries into different downstream processes, prompts, and tools.
- Routing easy questions to smaller models and hard questions to more capable models to optimize cost and speed.

### Workflow: Parallelization

LLMs can sometimes work simultaneously on a task and have their outputs aggregated programmatically. This workflow manifests in two key variations:

- **Sectioning**: Breaking a task into independent subtasks run in parallel.
- **Voting:** Running the same task multiple times to get diverse outputs.

**When to use this workflow:** Effective when the divided subtasks can be parallelized for speed, or when multiple perspectives or attempts are needed for higher confidence results.

**Examples:**

- **Sectioning**: Implementing guardrails where one model processes user queries while another screens them for inappropriate content. Automating evals for evaluating LLM performance.
- **Voting**: Reviewing code for vulnerabilities, where several different prompts review and flag the code if they find a problem. Evaluating whether content is inappropriate, with multiple prompts evaluating different aspects.

### Workflow: Orchestrator-workers

In the orchestrator-workers workflow, a central LLM dynamically breaks down tasks, delegates them to worker LLMs, and synthesizes their results.

**When to use this workflow:** Well-suited for complex tasks where you can't predict the subtasks needed (like in coding, where the number of files that need to be changed depends on the task). The key difference from parallelization is its flexibility—subtasks aren't pre-defined, but determined by the orchestrator based on the specific input.

**Examples:**

- Coding products that make complex changes to multiple files each time.
- Search tasks that involve gathering and analyzing information from multiple sources.

### Workflow: Evaluator-optimizer

In the evaluator-optimizer workflow, one LLM call generates a response while another provides evaluation and feedback in a loop.

**When to use this workflow:** Particularly effective when we have clear evaluation criteria, and when iterative refinement provides measurable value.

**Examples:**

- Literary translation where there are nuances that the translator LLM might not capture initially, but where an evaluator LLM can provide useful critiques.
- Complex search tasks that require multiple rounds of searching and analysis to gather comprehensive information.

### Agents

Agents are emerging in production as LLMs mature in key capabilities—understanding complex inputs, engaging in reasoning and planning, using tools reliably, and recovering from errors. Agents can handle sophisticated tasks, but their implementation is often straightforward. They are typically just LLMs using tools based on environmental feedback in a loop.

**When to use agents:** Agents can be used for open-ended problems where it's difficult or impossible to predict the required number of steps, and where you can't hardcode a fixed path. Agents' autonomy makes them ideal for scaling tasks in trusted environments.

**Examples:**

- A coding Agent to resolve SWE-bench tasks, which involve edits to many files based on a task description
- Computer use reference implementations, where Claude uses a computer to accomplish tasks

## Summary

Success in the LLM space isn't about building the most sophisticated system. It's about building the right system for your needs. Start with simple prompts, optimize them with comprehensive evaluation, and add multi-step agentic systems only when simpler solutions fall short.

When implementing agents, Anthropic tries to follow three core principles:

1. Maintain **simplicity** in your agent's design.
2. Prioritize **transparency** by explicitly showing the agent's planning steps.
3. Carefully craft your agent-computer interface (ACI) through thorough tool **documentation and testing**.

## Appendices

The article includes two appendices discussing promising applications for AI agents:

1. **Customer support** - Combining chatbot interfaces with enhanced capabilities through tool integration
2. **Coding agents** - For software development with verifiable outputs through automated tests

It also provides suggestions for tool engineering, emphasizing the importance of creating good agent-computer interfaces (ACI) with clear documentation and examples.
