# What is LangGraph

- **LangGraph** is an orchestration framework for building **intelligent, stateful, and multi-step** LLM workflows.

- It enables advanced features like **parallelism, loops, branching, memory, and resumability** — making it ideal for agentic and production-grade AI applications.

- It models your logic as a **graph of nodes** (tasks) and **edges** (routing) instead of a linear chain.


### LLM Workflows

1. **LLM workflows** are a step-by-step process using which we can build complex LLM applications.

2. Each step in a workflow performs a distinct task — such as prompting, reasoning, tool calling, memory access, or decision-making.

3. Workflows can be **linear, parallel, branched, or looped**, allowing for complex behaviours like retries, multi-agent communication, or tool-augmented reasoning.

4. **Common workflows**

Some of the common Workflows are: 

1. Prompt Chaining

![alt text](images/image_03_01.png)

2. Routing

![alt text](images/image_03_02.png)

3. Parallelization
Here task is divided. For e.g., Youtube video is being evaluated from multiple point of view. Is it following community guideline, any misinformation, sexual content etc. 

![alt text](images/image_03_04.png)

4. Orchestrator Workers: In orchestrator worker it is not predefined what work LLM 1 will do or second will do. e.g., Research Assistant, detailed research report based on the topic which LLM Call will take place LLM 1: AI research, LLM 2: Politics Research, LLM 2: Medical Research etc.
There is already an orchestrator LLM can decide seperate tasks to each LLM call. 

![alt text](images/image_03_05.png)

5. Evaluator Optimizer

![alt text](images/image_03_06.png)

## Graph Nodes and Edges

The system generates an essay topic, collects the student’s submission, and evaluates it in parallel on depth of analysis, language quality, and clarity of thought. Based on the combined score, it either gives feedback for improvement or approves the essay.

---

1. **GenerateTopic**  
   → System generates a relevant UPSC-style essay topic and presents it to the student.

2. **CollectEssay**  
   → Student writes and submits the essay based on the generated topic.

3. **EvaluateEssay (Parallel Evaluation Block)**  
   → Three evaluation tasks run in **parallel**:
   - **EvaluateDepth** – Analyzes depth of analysis, argument strength, and critical thinking.  
   - **EvaluateLanguage** – Checks grammar, vocabulary, fluency, and tone.  
   - **EvaluateClarity** – Assesses coherence, logical flow, and clarity of thought.  

4. **AggregateResults**  
   → Combines the three scores and generates a total score (e.g., out of 15).

5. **ConditionalRouting**  
   → Based on the total score:
   - If score meets threshold → go to **ShowSuccess**  
   - If score is below threshold → go to **GiveFeedback**  

6. **GiveFeedback**  
   → Provides targeted suggestions for improvement in weak areas.

7. **CollectRevision (optional loop)**  
   → Student resubmits the revised essay.  
   → Loop back to **EvaluateEssay**  

8. **ShowSuccess**  
   → Congratulates the student and ends the flow.


![alt text](images/image_03_07.png)

## State

In Langgraph, State is the shared memory that flows through your workflow - it holds all the data being passed between nodes as your graph runs. 

```python
essay_text: str
topic: str
depth_score: int
language_score: int
clarity_score: int
total_score: int
feedback: Annotated[list[str], add]
evaluation_round: int
```

TypedDict is used to create this key values pairs before initiating workflow.

## Reducers

Reducers in langgraph define how updates from nodes are applied to the shared state. 

Each key in the state can have its own reducers, which determines whether new data replaces, merges or adds to the existing value.

e.g., A chatbot simple LLM and user prompt require a add reduce to add user messages. 


# Langgraph Execution model 

Insipred from Google Pregel.

# 1. Graph Definition
You define:
- The **state schema**
- **Nodes** (functions that perform tasks)
- **Edges** (which node connects to which)

---

# 2. Compilation
You call `.compile()` on the `StateGraph`.  
This checks the graph structure and prepares it for execution.

---

# 3. Invocation
You run the graph with `.invoke(initial_state)`.  
LangGraph sends the initial state as a **message** to the entry node(s).

---

# 4. Super-Steps Begin
Execution proceeds in **rounds**.  
In each round (super-step):
- All **active nodes** (those that received messages) run in **parallel**  
- Each returns an **update** (message) to the state  

---

# 5. Message Passing & Node Activation
The messages are passed to downstream nodes via **edges**.  
Nodes that receive messages become active for the **next round**.

---

# 6. Halting Condition
**Execution stops** when:
- No nodes are active, and  
- No messages are in transit
