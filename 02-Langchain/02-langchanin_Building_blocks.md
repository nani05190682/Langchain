# LangChain Building Blocks — Detailed Explanation

LangChain helps you build applications that connect Large Language Models (LLMs) with prompts, conversation history, external knowledge, APIs, and tools.

Your diagram contains six core building blocks:

1. Model I/O
2. Chains
3. Memory
4. Tools
5. Retrieval
6. Agents

We'll explore each block using concepts, practical examples, Python code, and a common DevOps/SRE use case.

## Overview: How the six blocks fit together

Application

User asks: "Why is my Kubernetes pod restarting?"

Model I/O

Format the prompt and call the LLM

Retrieval

Find relevant runbooks

Tools

Query logs and metrics

Chains and Agents

Process evidence and coordinate the workflow

Response + Memory

Return findings and retain relevant conversation state

This is a conceptual flow, not a mandatory execution order. In a real application, an agent may call tools and retrieval multiple times, while a simple application may use only Model I/O.

## 1. Model I/O

Purpose: Handle communication between your application and the language model.

Model I/O covers the input sent to an LLM and the output received from it. In LangChain, this commonly involves chat models, prompt templates, and output parsers.

### Main components

- Chat models: Connect to an LLM such as OpenAI or a local model.
- Prompt templates: Build reusable instructions using variables.
- Output parsers: Convert responses into strings, structured objects, or other required formats.

### Example

Suppose an engineer wants an explanation of a Kubernetes error.

```
from langchain_openai import ChatOpenAIfrom langchain_core.prompts import ChatPromptTemplatefrom langchain_core.output_parsers import StrOutputParsermodel = ChatOpenAI(model="gpt-4.1-mini", temperature=0)prompt = ChatPromptTemplate.from_messages([    ("system", "You are a DevOps troubleshooting assistant."),    ("human", "Explain this Kubernetes error: {error}")])chain = prompt | model | StrOutputParser()result = chain.invoke({    "error": "CrashLoopBackOff"})print(result)
```

Install the required packages and configure `OPENAI_API_KEY` before running the example.

### What happens internally?

1. The prompt template inserts the supplied error.
2. LangChain formats the messages for the chat model.
3. The model generates a response.
4. The output parser returns the response as a string.

When to use it: Chatbots, text generation, classification, summarization, and any application that needs to communicate with an LLM.

## 2. Chains

Purpose: Connect multiple processing steps into a defined workflow.

A chain is useful when you know the steps your application should execute and want them to run in a predictable sequence.

### Example: Analyze a failed CI/CD pipeline

Input: Build log

Step 1: Extract the error

Step 2: Ask the LLM to analyze it

Step 3: Format the findings

Output: Error, explanation and suggested fix

### Example in Python

```
from langchain_core.prompts import ChatPromptTemplatefrom langchain_core.output_parsers import StrOutputParserfrom langchain_openai import ChatOpenAImodel = ChatOpenAI(model="gpt-4.1-mini", temperature=0)prompt = ChatPromptTemplate.from_template(    "Analyze this build error and suggest a fix:\n{log}")chain = prompt | model | StrOutputParser()result = chain.invoke({    "log": "ModuleNotFoundError: No module named 'requests'"})print(result)
```

The `|` operator connects the prompt, model, and parser into a runnable sequence.

### Types of processing workflows

- Sequential processing: Execute steps in order.
- Parallel processing: Execute independent tasks concurrently.
- Conditional processing: Select a path based on a condition.

LangChain's Runnable interfaces support composition, while more complex branching and orchestration can be implemented with other runnable components or LangGraph.

When to use it: Repeatable workflows such as incident summaries, document analysis, report generation, and CI/CD failure explanations.

## 3. Memory

Purpose: Preserve relevant information from previous interactions so an application can use it later.

Imagine asking an SRE assistant:

- First: “My application runs in namespace `payments`.”
- Next: “Why is it restarting?”

The assistant may need to retain the namespace from the earlier message to understand the follow-up.

### What can be retained?

- Conversation messages
- User preferences
- Previous workflow state
- Relevant facts retrieved from a data store

These are different kinds of information and may need different storage mechanisms.

### How it works

Previous conversation

Conversation history / State

Store or retrieve relevant messages

Current prompt

Combine the new question with relevant context

Context-aware response

### Modern LangChain approach

Older tutorials often use a dedicated `ConversationBufferMemory` class. In modern LangChain applications, conversation history and agent state are commonly managed through message history integrations and LangGraph persistence.

For example, LangGraph checkpointers can save conversation state between interactions associated with the same conversation ID.

Important: An LLM does not automatically remember every past conversation. The application must provide the relevant history or retrieve stored information.

When to use it: Conversational assistants, multi-step investigations, and workflows that must resume after interruption.

## 4. Tools

Purpose: Allow an LLM application to invoke external functions, services, or APIs.

An LLM can explain how to query Prometheus, but it cannot inspect your actual metrics unless your application gives it an appropriate integration.

A tool provides that connection.

### Common tools

| Tool              | Example operation                       |
| ----------------- | --------------------------------------- |
| Python function   | Calculate resource utilization          |
| Kubernetes API    | Retrieve pod status                     |
| Elasticsearch API | Search application logs                 |
| Prometheus API    | Query CPU and memory metrics            |
| Cloud API         | Inspect an AWS resource                 |
| Ansible           | Execute an approved automation workflow |

### Example: Create a tool

```
from langchain_core.tools import tool@tooldef get_pod_status(pod_name: str) -> str:    """Return the status of a Kubernetes pod."""    # Demo implementation; replace with a real,    # authorized Kubernetes API call.    return f"Pod {pod_name}: CrashLoopBackOff"print(get_pod_status.invoke({"pod_name": "payment-api"}))
```

This creates a callable tool. It does not yet give the LLM autonomous access to Kubernetes.

### How tool calling works

1. The application sends the user request to the model with available tool definitions.
2. The model may request a tool call.
3. The application or agent runtime executes the tool.
4. The result is returned to the model.
5. The model uses the result to produce its response or request another tool.

When to use it: Live data access, API integrations, calculations, monitoring, and controlled operational actions.

For production SRE use, enforce least-privilege permissions, validate tool arguments, and require approval for disruptive changes.

## 5. Retrieval

Purpose: Find relevant information from external knowledge sources and supply it to the LLM.

Retrieval is a major building block for RAG (Retrieval-Augmented Generation). It is particularly useful when you want an LLM to answer questions using your own documents rather than relying only on its training.

### Example: Search an internal SRE runbook

Suppose your company has 500 pages of Kubernetes troubleshooting documentation. An engineer asks:

> Why does my pod enter `CrashLoopBackOff` after deployment?

Instead of sending all 500 pages to the model, a retrieval system searches for relevant sections and supplies the best matches.

### RAG architecture

Knowledge sources

PDFs, runbooks, logs and documentation

Document loading and chunking

Extract text and divide it into smaller sections

Embeddings and vector store

Index document representations

User question → Retriever

Find relevant document chunks

LLM + retrieved context

Generate a grounded answer

### Main retrieval components

- Document loaders: Read files and other data sources.
- Text splitters: Break documents into chunks.
- Embeddings: Convert text into numerical vectors.
- Vector stores: Index and search those vectors.
- Retrievers: Return relevant documents for a query.

### Example code

```
from langchain.agents import create_agent
from langchain_openai import ChatOpenAI
from langchain_core.tools import tool

@tool
def check_service(service: str) -> str:
    """Check the status of a service."""
    # Demonstration only; replace with a real status check.
    return f"{service}: operational"

model = ChatOpenAI(model="gpt-4.1-mini")

agent = create_agent(
    model=model,
    tools=[check_service],
    system_prompt="You are an SRE assistant."
)

result = agent.invoke({
    "messages": [
        {"role": "user", "content": "Check the payment API"}
    ]
})

print(result["messages"][-1].content)
```

This example creates an in-memory vector index. For larger or persistent datasets, you would normally use a suitable persistent vector store. The code also requires OpenAI API access for embeddings.

When to use it: Enterprise knowledge assistants, document Q&A, internal support systems, and troubleshooting applications.

## 6. Agents

Purpose: Enable an LLM-powered application to choose and use tools dynamically to complete a task.

A chain typically follows a predefined sequence. An agent can decide which available tool to use next based on the request and the results it receives.

### Chain vs. Agent

| Chain                                         | Agent                                            |
| --------------------------------------------- | ------------------------------------------------ |
| Follows a defined workflow                    | Selects tools dynamically                        |
| Execution sequence is generally predetermined | May perform several reasoning and tool-use steps |
| Useful for repeatable processing              | Useful for less predictable tasks                |
| Example: Summarize a build log                | Example: Investigate a production alert          |

### Example: AI SRE agent

An engineer asks:

> Investigate why the payment API is slow.

The agent might:

1. Query Prometheus for latency and CPU metrics.
2. Search Elasticsearch for recent errors.
3. Retrieve the relevant troubleshooting runbook.
4. Compare the evidence and identify a likely cause.
5. Generate a report and recommend remediation.
6. Ask an engineer to approve any operational change.

User request

Agent / LLM

Choose an appropriate next step

Prometheus

Elasticsearch

Runbooks

Analyze evidence

Choose the next step if more information is needed

Report + approval-gated remediation

### Modern LangChain agent example

```
from langchain.agents import create_agentfrom langchain_openai import ChatOpenAIfrom langchain_core.tools import tool@tooldef check_service(service: str) -> str:    """Check the status of a service."""    # Demonstration only; replace with a real status check.    return f"{service}: operational"model = ChatOpenAI(model="gpt-4.1-mini")agent = create_agent(    model=model,    tools=[check_service],    system_prompt="You are an SRE assistant.")result = agent.invoke({    "messages": [        {"role": "user", "content": "Check the payment API"}    ]})print(result["messages"][-1].content)
```

The tool above is a demonstration, not a live infrastructure check. A production implementation needs an actual API integration and appropriate access controls.

When to use it: AI assistants that investigate incidents, call multiple APIs, perform multi-step research, or coordinate operational workflows.

## 7. How the six blocks differ

| Block     | Key question it answers                                          |
| --------- | ---------------------------------------------------------------- |
| Model I/O | How do I send information to an LLM and receive its response?    |
| Chains    | How do I connect processing steps?                               |
| Memory    | How do I retain relevant conversation history or workflow state? |
| Tools     | How can the application interact with external systems?          |
| Retrieval | How can the model access relevant external knowledge?            |
| Agents    | How can the model choose tools and steps to pursue a goal?       |

These are complementary concepts, not six mandatory stages in every LangChain application.

## 8. Suggested hands-on learning sequence

For your AI SRE project, I recommend building these small projects in order:

Lab 1 — LLM chatbot

Learn Model I/O, prompts, and chains.

Lab 2 — Chat with runbooks

Learn retrieval, embeddings, and RAG.

Lab 3 — Log-analysis tool

Connect a Python function or Elasticsearch API.

Lab 4 — AI SRE agent

Combine retrieval and tools into an agent with approval-gated remediation.

The goal is to understand each component independently before combining them into a production-style application.
