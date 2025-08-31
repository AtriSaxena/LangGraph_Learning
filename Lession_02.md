## LangChain VS LangGraph 

### LangChain Recap

## What is LangChain

LangChain is an open-source library designed to simplify the process of building LLM based applications.

It provides modular building blocks that let you create sophisticated LLM-based workflows with ease.

LangChain consists of multiple components:

1. **Model** components give us a unified interface to interact with various LLM providers.
2. **Prompts** component helps you engineer prompts.
3. **Retrievers** component helps you fetch relevant documents from a vector store.

But the biggest offering of LangChain is **Chains**.

---

## What can you do with LangChain

1. Simple conversational workflows like Chatbots, Text Summarizers  
    - e.g., A loop chatbot taking prompt from user and pass to LLM.
2. Multistep workflows
    - E.g., Take a simple topic from user and make a detailed report on that and generate summary on that. 
3. RAG applications  
    - E.g., External Document or External knowledge want to add like some local policies. 
4. Basic level agents
    - E.g., Weather based LLM using different tools like weather api tool.


## Automated Hiring Workflow example: 

![alt text](images/image_02_01.png)

A detailed flowchart is created to do an Automatic Hiring. We will study the flowchart and understand if we can implement in Langchain. If we can then what are the challenges we will face? 

*Note: This is not an Agentic AI Application, This is just a Workflow.*

**Difference between AgenticAI & Workflow:** https://www.anthropic.com/engineering/building-effective-agents

- **Workflows** are systems where LLMs and tools are orchestrated through predefined code paths.
- **Agents**, on the other hand, are systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks.

### Challenges (If implementing workflow in langchain than in Langgraph)

#### 1. Control Flow Complexity: 

Flow contains different complexity as:
- Conditional branch: If certain condition is there flow is shifted towards one direction or the other.
- Loops: If JD not approved, we keep looping untill it is approved. 
- Jumps: Moving back to previous state like Wait 48 Hours then Monitor application.

Example showing Langchain code for part of block: 

![alt text](images/image_02_02.png)

*Langchain Code:*
![alt text](images/image_02_03.png)

Step 5 in the above diagram require lot of glue code in python which will be difficult to maintain in the large application.
While in langgraph nodes and edges are there where each nodes are python function. No custom python code requires. 

### 2. Handling State: 

![alt text](images/image_02_03.png)

State is important to store import data about the worflow. State tells what is current state and values of previous states and next where flow will go. 

Langchain has concept of memory but only to store conversation,but state data cannot stored in the memory. 

In langgraph, state object dictionary is created and accessible by each node of the graph.

### 3. Event Driven Execution

Some events which require pause and play like JD approval, or wait 7 days, etc where langchain cannot be used. Since, langchain execute sequentially. 

In langgraph, you can use checkpointer to pause and resume from the point it was pause. 

### 4. Fault Tolerance

Since, workflow are long running process there is high possibility of getting fault. There can be two types of fault: 
- Small fault like API not working
- big (system down)

Langchain is a short execution process any fault in the workflow leads to restart of the entire process. 
Langgraph there are option to recover the process after some time and states also saved. 
Even if system is down, then also checkpointer helps to recover. 

### 5. Human in the loop: 

Human input again require pause and play of the worfklow execution. 
Langgraph allows you to pause execution indefinately - for minutes, hours or even days - until human input is recieved. 

### 6. Nested Workflows

A single node can be define as a subgraph. 
Multiagent based system can also be built. 

### 7. Observability

Observability refers to how easily you can monitor, debug and understand what your workflow is doing at runtime.
Tool like LangSmith is used. 
Langsmith can track langchain code but glue code like loop, jump etc cannot be monitor.
While Langsmith is tightly integrated with Langgraph.

## Conclusion

# 1. What is LangGraph?

**LangGraph** is an orchestration framework that enables you to build **stateful**, **multi-step**, and **event-driven** workflows using large **language models (LLMs)**. It’s ideal for designing both **single-agent** and **multi-agentic** AI applications.

Think of LangGraph as a **flowchart engine for LLMs** — you define the **steps** (nodes), how they’re connected (**edges**), and the logic that governs the transitions. LangGraph takes care of **state management, conditional branching, looping, pausing/resuming, and fault recovery** — features essential for building robust, production-grade AI systems.

---

# 2. When to Use What?

- Use **LangChain** when you're building **simple, linear workflows** — like a prompt chain, summarizer, or a basic retrieval system.

- Use **LangGraph** when your use case involves **complex, non-linear workflows** that need:
  - Conditional paths
  - Loops
  - Human-in-the-loop steps
  - Multi-agent coordination
  - Asynchronous or event-driven execution


# 3. Should We Still Use LangChain?

**Yes.** LangGraph is built **on top of LangChain** — it doesn’t replace it.  

You’ll still use **LangChain components** like:
- `ChatOpenAI` (LLMs)
- `PromptTemplate`
- `Retrievers`
- `DocumentLoaders`
- `Tools`, etc.

---

LangGraph handles **workflow orchestration**, while LangChain provides the **building blocks** for each step in that workflow.
