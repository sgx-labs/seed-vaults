---
title: "How Do I Build Multi-Agent Teams with CrewAI?"
tags: [CrewAI, multi-agent, roles, crews, orchestration]
content_type: research
domain: engineering
---

# How Do I Build Multi-Agent Teams with CrewAI?

## The Question

What are the practical patterns for building multi-agent systems with CrewAI?

## Core Concept: Agents as Roles

CrewAI models agent systems as teams (Crews) of role-based agents. Each agent has a role, backstory, and goal. The framework handles task assignment and coordination.

```python
from crewai import Agent, Task, Crew, Process

# Define agents by role
researcher = Agent(
    role="Technical Researcher",
    goal="Find accurate, current information about the given topic",
    backstory="You are a senior researcher with expertise in software architecture. "
              "You are thorough and always cite your sources.",
    tools=[web_search, doc_search],
    verbose=True,
)

writer = Agent(
    role="Technical Writer",
    goal="Write clear, accurate technical documentation",
    backstory="You are an experienced technical writer who explains complex "
              "concepts in simple terms.",
    tools=[file_write],
    verbose=True,
)

reviewer = Agent(
    role="Technical Reviewer",
    goal="Ensure accuracy and completeness of technical content",
    backstory="You are a senior engineer who reviews documentation for "
              "technical accuracy and clarity.",
    tools=[file_read],
    verbose=True,
)
```

## Key Patterns

### Sequential Crew

Tasks execute in order. Each agent's output feeds the next:

```python
research_task = Task(
    description="Research the current best practices for API rate limiting",
    agent=researcher,
    expected_output="A comprehensive summary of rate limiting approaches",
)

write_task = Task(
    description="Write a guide based on the research findings",
    agent=writer,
    expected_output="A clear, well-structured technical guide",
    context=[research_task],  # Gets output from research
)

review_task = Task(
    description="Review the guide for technical accuracy",
    agent=reviewer,
    expected_output="Reviewed guide with corrections noted",
    context=[write_task],
)

crew = Crew(
    agents=[researcher, writer, reviewer],
    tasks=[research_task, write_task, review_task],
    process=Process.sequential,
)

result = crew.kickoff()
```

### Hierarchical Crew

A manager agent coordinates worker agents:

```python
crew = Crew(
    agents=[researcher, writer, reviewer],
    tasks=[research_task, write_task, review_task],
    process=Process.hierarchical,
    manager_llm="claude-sonnet-4-5-20250929",
)
```

The manager decides task order, assigns agents, and aggregates results.

### Task Delegation

Agents can delegate subtasks to other agents:

```python
researcher = Agent(
    role="Lead Researcher",
    goal="Coordinate research efforts",
    allow_delegation=True,  # Can delegate to other agents
)
```

### CrewAI Flows (Production Architecture)

For complex workflows, use Flows to compose multiple Crews:

```python
from crewai.flow.flow import Flow, start, listen

class ResearchFlow(Flow):
    @start()
    def begin_research(self):
        return research_crew.kickoff()

    @listen(begin_research)
    def write_report(self, research_results):
        return writing_crew.kickoff(inputs={"research": research_results})

    @listen(write_report)
    def review_report(self, draft):
        return review_crew.kickoff(inputs={"draft": draft})

flow = ResearchFlow()
result = flow.kickoff()
```

## When CrewAI Is the Right Choice

- Your problem maps naturally to team roles (researcher, writer, reviewer)
- You want quick multi-agent prototyping
- Sequential or hierarchical task flows
- You prefer declarative agent definitions over code-heavy orchestration

## Limitations

- Less control over execution flow than LangGraph
- Role-based metaphor can be limiting for some architectures
- Performance overhead from agent-to-agent communication
- Debugging multi-agent conversations can be challenging

## See Also

- `hub/frameworks.md` — Framework comparison
- `research/architecture/multi-agent-communication.md` — Communication patterns
- `research/architecture/single-vs-multi-agent.md` — When to use multi-agent
