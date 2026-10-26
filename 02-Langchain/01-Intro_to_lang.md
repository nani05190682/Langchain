# What is LangChain?

LangChain is an open-source framework that helps you build applications powered by Large Language Models (LLMs) by connecting models to prompts, documents, APIs, databases, and external tools.

For example, instead of simply asking an LLM a question, you can use LangChain to build an AI assistant that reads your Linux logs, identifies errors, retrieves relevant documentation, and suggests troubleshooting steps.

## 1. Why do we need LangChain?

An LLM by itself has limitations:

- It may not know your private company documents or infrastructure logs.
- It cannot automatically access your databases or APIs.
- It doesn't inherently know how to execute your operational workflows.
- Its knowledge may not include the latest information.

LangChain helps connect the LLM to the data and tools needed to build a useful application.

## 2. LangChain architecture

1\. User / Application

Question, prompt or request

2\. LangChain

Prompt templates · Model integration · Retrieval · Tool calling

3\. LLM

OpenAI · Anthropic · Local models via Ollama

4\. External resources (when needed)

Vector databases · APIs · Files · Monitoring tools · Linux commands

5\. Response

Answer, analysis or proposed action

Note: This is a conceptual architecture. A LangChain application can call tools or retrieve data before, during, or after model inference, depending on its design.

## 3. Key building blocks of LangChain

| Component        | Purpose                                        | Example                        |
| ---------------- | ---------------------------------------------- | ------------------------------ |
| Models           | Connect to an LLM                              | OpenAI, Ollama                 |
| Prompts          | Structure instructions for the LLM             | “Analyze this error log”       |
| Output parsers   | Convert responses into structured data         | JSON output                    |
| Document loaders | Load data from files and other sources         | PDF, text, web pages           |
| Text splitters   | Divide documents into smaller chunks           | Split a 100-page manual        |
| Embeddings       | Convert text into vectors                      | Create document vectors        |
| Retrievers       | Find relevant information                      | Search internal runbooks       |
| Tools            | Allow the model to invoke external functions   | Query Prometheus               |
| Agents           | Decide which tools to use and in what sequence | Investigate a Kubernetes alert |

## 4. Your first LangChain program

Let's connect LangChain to the OpenAI API and ask a simple question.

### Step 1: Install the libraries

```
python3 -m venv .venv
source .venv/bin/activate

pip install langchain langchain-openai
```

### Step 2: Set your API key

```
export OPENAI_API_KEY="your_openai_api_key"
```

### Step 3: Create `app.py`

```
from langchain_openai import ChatOpenAIfrom langchain_core.prompts import ChatPromptTemplate# Initialize the language modelmodel = ChatOpenAI(model="gpt-4.1-mini")# Define the promptprompt = ChatPromptTemplate.from_messages([    ("system", "You are a helpful DevOps assistant."),    ("human", "{question}")])# Connect the prompt to the modelchain = prompt | model# Ask a questionresponse = chain.invoke({    "question": "What is Kubernetes?"})print(response.content)
```

### Step 4: Run the program

```
python app.py
```

The program sends your question to the configured OpenAI model and prints its response. This requires a valid API key and API access to the selected model.

## 5. LangChain vs. LangGraph vs. LangSmith

These technologies serve related but different purposes.

LangChain

Build LLM applications using models, prompts, retrieval and tools.

LangGraph

Build stateful, multi-step agent workflows with branching, persistence and human approval.

LangSmith

Trace, evaluate, test and monitor LLM applications.

## 6. A practical DevOps/SRE use case

Imagine you have Prometheus, Grafana, ELK and Kubernetes running in your environment.

You could build an AI SRE assistant with this workflow:

1. Prometheus detects a high CPU alert.
2. LangChain connects the model to a tool that retrieves relevant metrics.
3. The model requests recent logs from ELK.
4. It correlates the logs and metrics and proposes a likely root cause.
5. The assistant recommends a remediation, such as scaling a deployment.
6. An authorized engineer approves the action before it executes.

For a production system, implement access controls, command allowlists, audit logging and approval gates. The LLM should not receive unrestricted shell access.

## 7. Recommended learning path

Given your DevOps and cloud background, I suggest learning in this order:

1. LLM fundamentals and prompt engineering
2. LangChain models, prompts and output parsers
3. Document loading, embeddings and vector databases
4. Retrieval-Augmented Generation (RAG)
5. Tool calling and structured outputs
6. LangGraph and agent workflows
7. Build a local AI SRE assistant using Ollama, ELK and Prometheus

The key distinction: An LLM is the language model; LangChain is a framework for building applications around it; an AI agent is an application that can use a model and tools to pursue a goal. LangChain can help build agents, but it is not itself an LLM.



# Why do we need LangChain?

LangChain helps you turn an LLM into a practical AI application. Instead of writing all the integration code yourself, you can use a framework to connect an LLM with your data, APIs, databases, and tools.

Let's understand this with a DevOps example.

## 1. Without LangChain vs. with LangChain

Imagine you want to build an AI assistant that analyzes Kubernetes errors.

Without LangChain

You build the integration yourself

Python application

Write API calls and prompt handling

Implement tool execution, parsing and error handling

With LangChain

Reuse existing integrations and abstractions

Python application

LangChain model, prompt and tool integrations

LLM + Kubernetes API + log search

You can build the same application without LangChain. LangChain makes many common integrations and workflows easier to develop and maintain.

## 2. Five reasons to use LangChain

1\. Connect different LLM providers

Integrate supported models from OpenAI, Anthropic and local model runtimes such as Ollama through common interfaces.

2\. Give LLMs access to your data

Load documents, retrieve relevant content from vector databases and implement RAG so an LLM can answer questions about your internal runbooks.

3\. Enable tool calling

Connect the model to Python functions, REST APIs, monitoring systems and other approved tools.

4\. Build multi-step workflows

Combine prompts, model calls, retrieval and tools. For more complex stateful agent workflows, you can use LangGraph.

5\. Simplify application development

Reuse components instead of repeatedly writing boilerplate for model integrations, message formats and structured responses.

## 3. Real-world example: AI SRE assistant

Suppose Kubernetes reports `CrashLoopBackOff`.

| Step | What happens              | LangChain's role                            |
| ---- | ------------------------- | ------------------------------------------- |
| 1    | Receive the alert         | Pass alert details into the application     |
| 2    | Retrieve pod logs         | Invoke an approved log-search tool          |
| 3    | Read internal runbooks    | Retrieve relevant documentation             |
| 4    | Analyze the evidence      | Send context to the LLM                     |
| 5    | Recommend a fix           | Generate an explanation and proposed action |
| 6    | Request engineer approval | Integrate with an approval workflow         |

For example, the assistant might discover that a container repeatedly exits because a required environment variable is missing. It can recommend a correction based on the logs and runbook rather than guessing from the alert alone.

## 4. Do you always need LangChain?

No. You should choose the simplest tool that fits your requirements.

| Requirement                                   | Suitable approach                           |
| --------------------------------------------- | ------------------------------------------- |
| Ask an LLM a simple question                  | Direct model API                            |
| Build a small chatbot                         | Direct API or LangChain                     |
| Search internal documents using RAG           | LangChain or custom Python                  |
| Build a stateful AI agent with multiple steps | LangGraph or another workflow framework     |
| Call one REST API from Python                 | Standard Python libraries may be sufficient |

LangChain also introduces abstractions and dependencies, so it is worth learning the underlying model API rather than treating the framework as mandatory.

## 5. The big picture

LLM

Understands context and generates responses

LangChain

Connects the model with prompts, data and tools

Your infrastructure

Kubernetes, Linux, ELK, Prometheus and cloud APIs

AI-powered SRE application


# Key Use Cases of LangChain

LangChain is used to build applications that connect LLMs with enterprise data, APIs, databases, and tools. For your DevOps, Cloud, and SRE background, some of the most valuable applications are AI-powered troubleshooting, log analysis, RAG, and infrastructure automation.

## 1. Top 8 use cases

[DocuData AI - Intelligent On-Premise Chatbot | €29/month](https://images.openai.com/static-rsc-4/X-6VAdHV1pdCrvTG492tzBU9MdgV5RNuzsoDTfjllNOjCObcmG9eYCiZKop4uK2bGhtayp5WQWvMxVw1brkY6FKB68zktEIF3EkAKMbjUAOHvWP3ePBPmlZApQ0J1_hK9RrYcEPv_DFUuNLLAGXcD9_nv22pbsnzfCHbU2QfccU?purpose=inline)

1\. AI Chatbots and Virtual Assistants

Build conversational assistants that answer questions, maintain conversation context, and interact with users.

Example: Internal IT helpdesk assistant for employee support.

[Innovative AI: The Power of Retrieval Augmented Generation](https://images.openai.com/static-rsc-4/oVot2ra_e86j8tiHtUoiGCw8TJgJGFb89lVmWCtXi3IXzRDD431PxtPDayJeuAUqKBfm2me0Hq52NNnuBjd7T5bGjnTx7KlS_KBjbAmm8WqmzKf2X1f3p6KbD6lk57wPwkuLGLuD4LyiLGPBDfD6fsOBG0MFuzMTwz6DZ6oD2lk?purpose=inline)

2\. Retrieval-Augmented Generation (RAG)

Retrieve relevant information from documents and provide it to an LLM as context before generating an answer.

Example: Ask questions about PDF manuals, architecture documents, or company runbooks.

[Datadog Pricing, Cost & Reviews - Capterra UK 2026](https://images.openai.com/static-rsc-4/n3Emk93rlD9lVPRPiXp48LFYHiqXCET9FcnpxZuUcT2jd4RZ_jTKFC1YjlfSnrbk0OuFCex0NT60tBLCyCWSYgS6fe32qlO3c2hvz38LmpPgadm4P0q8RwEgxxSM0alYmvtZ6675pkTSgGgZHc_ZLgcGo4HIrDNBuHX5i4ipu-8?purpose=inline)

3\. AI-Powered Log Analysis

Connect an LLM to log-search tools to summarize errors, identify recurring patterns, and suggest possible root causes.

Example: Analyze ELK logs to investigate a Kubernetes CrashLoopBackOff incident.

[From APIs to Automation: Building End-to-End Connected Business Workflows](https://images.openai.com/static-rsc-4/bMA945W1I8ICE_9Vn3WHOBTn2bQyCkebpeOLQrJxsoIJ3WvIT-kTSWmrF1iGcdqBd22VmOVlfLG-yk9P7u6KQ4t3J3HUjpYlhqhe3ykJ7LdfOYapPjqZpwsWyaAMOo6lYGTEAXZhQSytdRmpijvp3GppDFRTbAQkkklKmnwtOYM?purpose=inline)

4\. AI Agents and Tool Calling

Let a model select and invoke approved tools to complete multi-step tasks.

Example: Query Prometheus, retrieve pod logs, and prepare a troubleshooting report.

[Software Code Orchestration Agents Transform DevOps and CI/CD - AI CERTs News](https://images.openai.com/static-rsc-4/5JP2tI-_rK2p_lBkjhA0h3uhzm4bf2hhD_K1MFbff8a--hRH4TrBGSief2rBd5edOruhEpxGGUCfsx-_8WmP4kAK-bVJsf1X0c0PMIPsoX_RbkrVg3U1xY16TzRhg5FZkkXeHc0ifQ4ENQ2mmHZOsPTcUXUfmXe-tcqBv7pR0ko?purpose=inline)

5\. DevOps and CI/CD Automation

Use LLMs to explain pipeline failures, summarize build errors, and propose fixes through GitHub Actions, GitLab CI, or Jenkins integrations.

Example: Analyze a failed Terraform pipeline and recommend a correction.

[Building Production Grade IDP with OCR, Vision LLMs and Agents \[Includes Code\] | by Edge Case | Medium](https://images.openai.com/static-rsc-4/-bxlwhYetq1p_QWbcPrH23mvCXc_ySmggD1gBQhCrDI9h8AS3iOPYEzFwLhQ4uQ3Ux3Jf_XV7a_GekIDfK543L5jYWzqZoCHu8B91As5-O3dwo6B1LNh1nCRphOlpEvq_Qm-SgF-cKH0g12WeEHDDUGa4hm5aATVb9uiPoe51yY?purpose=inline)

6\. Document Processing and Summarization

Extract information, summarize lengthy reports, classify text, and convert unstructured documents into structured output.

Example: Generate a summary of a cloud architecture assessment or incident report.

[Crushing your AI Hackathon on Databricks — 10 ideas | by AI on Databricks | Medium](https://images.openai.com/static-rsc-4/WnOMpZGB4c2c_v_uV-GSXGHFzzpE7S2k-pDW1RfbylkBYA8p34GyBvnSgm7nlkTsTVjra49tfrcLqACr8HUNSXspagg32gehnMArHeA2CBLvxo8D97I6phXzW2NN_6A_jCq_wyJAk8YM6337kjfUYnnYEoTXGNst_B2aOulINuI?purpose=inline)

7\. Natural-Language Data Analysis

Connect an LLM to approved database queries and analytical tools to answer questions about data.

Example: “Which Kubernetes namespaces had the highest CPU consumption yesterday?”

[Projects | Sami Shafique](https://images.openai.com/static-rsc-4/i2u0vcSj4XChz8BU28fShCSFqO98KHcIbYfZ0CnQdmmjWZMkskliaKzBt5sOQ0iUgYWr6C5loTIwgmy_7KuTijzhHbVCoJmbLok25m_ckDG1eArbH8k1C7WEHCl-M3YKLDAfITVIiVaVC1_PFkNVZlxZBjku1SLVpJwgiFlDyMo?purpose=inline)

8\. Cloud and Infrastructure Operations

Integrate an LLM with cloud APIs and infrastructure automation tools to inspect resources, explain configuration issues, and propose changes.

Example: Query Proxmox inventory, identify unused VMs, and prepare a cleanup plan for approval.

## 2. Which LangChain use cases are most valuable for SRE?

For an AI-powered SRE platform, I would prioritize these five projects.

| Priority | Project                          | Technologies                                       |
| -------- | -------------------------------- | -------------------------------------------------- |
| 1        | Chat with ELK logs               | LangChain, OpenAI or Ollama, Elasticsearch         |
| 2        | Internal runbook assistant       | LangChain, embeddings, vector database             |
| 3        | Kubernetes incident investigator | LangChain tools, Kubernetes API, Prometheus        |
| 4        | CI/CD failure analyzer           | LangChain, GitLab/GitHub APIs                      |
| 5        | Infrastructure operations agent  | LangGraph, Proxmox API, Ansible, approval workflow |

## 3. How the components work together

Here is a typical architecture for an AI SRE assistant.

Alert or User Query

Prometheus alert or engineer question

LangChain Application

Prompt templates · Model · Tools · Retrieval

Elasticsearch

Logs and events

Prometheus

Metrics and alerts

Vector Database

Runbooks and documents

Infrastructure APIs

Kubernetes and Proxmox

LLM Analysis

Correlate evidence and recommend a root cause

Engineer Approval

Review and authorize any remediation

## 4. LangChain vs. LangGraph: which should you use?

- LangChain: Start here for model integration, RAG, document processing, and tool calling.
- LangGraph: Add this when you need stateful workflows, multiple agent steps, retries, checkpoints, or human approval.
- LangSmith: Useful for tracing, evaluating, and debugging LLM applications.

One important principle: LangChain does not automatically make an application autonomous. You must implement the tools, permissions, workflow logic, and safety controls.

My recommendation: Start with a Chat with ELK Logs project. It introduces LLM integration, retrieval, tool calling, and practical SRE use cases without requiring you to begin with a fully autonomous agent.
