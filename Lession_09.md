## Persistence in LangGraph

Persistence in Langgraph refers to the ability to save and restore the state of a workflow over time. 

In LangGraph we store the graph state.
GraphState will be saved at each step of graph node. 

But without persistence we get only final state. 
But with persistence we get state result at each step of node. 

```
START -> NODE 1 -> NODE 2 -> END
[num: 1] -> [num: 2] -> [num: 3] -> [num: 4]

Final state: num: 4
Intermediate state: 
[1, 2, 3, 4]
```

- Persistence helps in development of chatbots. Generally, whenever you goto chatbot there are options to choose new chat or continue old chat. 
- Therefore to resume old chat old messages needs to be feteched from the database.


### Checkpointers in Persistence

- At every superstep, checkpoint is created. At each node intermediate and final state will be saved. 

![alt text](images/image_09_01.png)

```
state: numbers[list[int], add]

CP1 -> numbers[1]
CP2 -> numbers[1,2]
CP3 -> numbers[1,2,3,4,5]
CP4 -> numbers[1,2,3,4,5]

All values saved into checkpoint.
```

### Threads in Persistence

- In LangGraph, a thread represents the stateful execution context of a graph — in other words, it’s a container that holds the state, memory, and intermediate results of a single "run" of your graph.

When you enable persistence, LangGraph stores each thread's state in a database (like Postgres, SQLite, etc.), so that you can resume it later, inspect past states, or continue a conversation even after a restart.

#### Thread = State + History
Each thread stores:

- Current state of all nodes in the graph

- Inputs & outputs of previous steps

- Metadata (like user ID, timestamps)

- Custom memory (chat history, variables, etc.)

#### Persistence = Durable Storage
Without persistence, threads only exist in memory and are lost when the process restarts.
With persistence, threads are stored in a database so you can:

- Resume execution where you left off

- List all threads (e.g., for a user)

- Query past results (e.g., audit what the agent did)

#### Each User Session = One or More Threads
If you're building a chatbot:

- Each conversation with a user can be one thread.

- Or you can have multiple threads per user for different topics.

#### Why Threads Are Useful

✅ Stateful Conversations – Your LLM agent remembers past steps, variables, and chat history.

✅ Long-Running Workflows – You can pause a workflow (e.g., waiting for a human approval) and resume later.

✅ Multi-User Support – You can maintain separate state per user or per task.

✅ Debugging – You can inspect a thread’s state step by step to debug.

✅ Scalability – Since threads are persisted, multiple servers can pick up where others left off.



### CODE example

![alt text](images/image_09_02.png)


### Benefits of Persistence: 

- Short Term Memory
    - Resume pass conversation to the chatbot
- Fault Tolerance
    - If there are three steps in the workflow and at step 2 our workflow crashes. You can resume the workflow directly from Step 2 need not to run step 1 or from start. 
    - Code: `Code/Lession_10_FaultTolerance.ipynb`
- Human in the Loop
    - It is required to interrupt the session when Human is required in the loop. Since, workflow don't know when human will respond. Therefore, interrupt is added. 
- Time Travel (Jumping directly to a state/node)
    - You can go at certain checkpoint and replay the flow from certain node. 
    - Very helpful in debugging the workflow code.