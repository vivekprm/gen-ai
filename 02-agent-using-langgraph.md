Let's breakdown what we did in lesson 1.

<img width="645" alt="Screenshot 2025-01-30 at 11 19 33 AM" src="https://github.com/user-attachments/assets/88a55282-5e1e-47a8-8548-961cdd6b4770" />

We had this user message that came in, we then had the system prompt and this was very long one. 
We then called an LLM with that. This outputted somthing like what's right there a thought and an action.
Based on that we then made a decision, we would either return or we would call a tool. and we put this on a big loop i.e. query funtion and we had these two tools that we would call "calualte" and "average_dog_weight".

If we call the tool we would get back an observation and then we would loop back in, to the prompt, and we would put that observation as a new message.

# LangChain Prompts
Let's break this down into **LangChain** components.

First let's talk about prompts. So **prompt templates** allow reusable prompts and what this basically means is that we can create something like this:

```python
from langchain.prompts import PromptTemplate
prompt_template = PromptTemplate.from_template(template="Tell me a {adjective} joke about {content}")
```

A little string with some formatted variables that we can replace. And so those formatted variables can come from user content. So we can have this prompt template that we format in different ways depending on the user content.

If you want to see a bunch of examples of these prompt templates, you can actually see them in the [LangChain hub](https://smith.langchain.com/hub).

[Here](https://smith.langchain.com/hub/hwchase17/react) is the one that is very similar to the agent prompt that we just used. Here we don't hardcode the tools.

```
Answer the following questions as best you can. You have access to the following tools:

{tools}

Use the following format:

Question: the input question you must answer
Thought: you should always think about what to do
Action: the action to take, should be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (this Thought/Action/Action Input/Observation can repeat N times)
Thought: I now know the final answer
Final Answer: the final answer to the original input question

Begin!

Question: {input}
Thought:{agent_scratchpad}
```

The next component in LangChain that we have is tools. Here is the **tavily** tool. This is the search that we will be using from here on out.

```python
from langchain_community.tools.tavily_search import TavilySearchResults
tool = TavilySearchResults(max_results=2)

self.tools = {t.name: t for t in tools}
self.model = model.bind_tools([tool])
```

You can see that we imported it from langchain community pakage which contains hundreds of other tools.

May be biggest part of this application that most code that we wrote, was this function that would loop and that's what is represented by all these arrows and that's one way to think about **LangGraph**.

<img width="889" alt="Screenshot 2025-01-30 at 11 44 23 AM" src="https://github.com/user-attachments/assets/dc54f1f0-5cc8-4aad-967a-972e8c3dec99" />

**LangGraph** helps you describe and orchestrate that control flow. Specifially, it allows you to create **cycli graphs**, which is exactly what we have here. It also comes with built-in **persistence** and this is really nice for having multiple conversations at the same time or remembering previous iterations and actions.

This **persistence** also enables really cool human in the loop features.

<img width="913" alt="Screenshot 2025-01-30 at 11 53 59 AM" src="https://github.com/user-attachments/assets/a4fe66fa-205e-4a84-9d6e-bc5e9c00b40c" />

If you look at these diagrams, and these are all diagrams of agents from academic papers, these are all represented as graphs and that realization was what led to the creation of **LangGraph** and extension of **LangChain** speifially aimed at agent and multi-agent flows. Crucially it allows for really controlled flows.
So in these diagrams there's really specific arrows leading from one box or state to the next. We have seen that this controllability is crucial for creating agents that can perform well.  

## Graphs
Three of the core concepts of **LangGraph** are 
- **Nodes**
- **Edges**
- **Conditional Edges**.

Nodes are the Agents or Functions .
Edges connect these nodes .
Conditional edges are used when you need to make decision about which node you should go to next.

Look at the example of Langgraph in the notebook which is equivalent to the code we wrote in lesson 1.

<img width="829" alt="Screenshot 2025-01-30 at 12 06 08 PM" src="https://github.com/user-attachments/assets/c112a104-f42a-48fd-b7bd-28e404e401c1" />

We can have an agent node, this is the LLM.
We can then have a conditional edge which takes the result of that LLM call and decides what to do next.
One of those edges can be an action edge which calls a function node, and that automatically loops back to the agent node.
There is an entrypoint, which is where you start, and then there is an end node, whih is the other action available to take after the agent.

<img width="711" alt="Screenshot 2025-01-30 at 12 07 38 PM" src="https://github.com/user-attachments/assets/61af0a4a-95c6-4579-aaf8-5a3c55a1445a" />

One of the most important things to understand when working with **LangGraph** is the state that is tracked over time. This is often called the **Agent State**. This is accessible at all parts of the graph at each node in eah edge. It is local to the Graph and it an be stored in persistence layer, meaning that you can resume with that state at any point in time later on.

Looking at two examples:
Simple State:
```python
class AgentState(TypedDict):
    messages: Annotated[Sequence[BaseMessage], operator.add]
```

This message variable is a sequence of BaseMessage. **BaseMessage** is a LangChain type. It's annotated with **operator.add** which means that when the state is updated with new messages, it doesn't override the existing messages but rather it adds them to that state.

Complex State:
```python
class AgentState(TypedDict):
    input: str
    chat_history: list[BaseMessage]
    agent_outcome: Union[AgentAction, AgentFinish, None]
    intermediate_steps: Annotated[list[tuple[AgentAction, str]], operator.add]
```

Three of the variables are not annotated in anyway. This means that when a new update is pushed to that variable, it overrides the existing value. However, **intermediate_steps** is annotated with **operator.add** which means that when a new update is pushed to that variable, it adds it to the existing value. This makes sense because **intermediate_steps** is what is tracked as the agent action and agent observation throughout the graph as it executes and so we want to continuously add to that as more and more actions are taken.

Getting really specific about the previous agent that we created and how that would map to a **LangGraph** object:

We can see that we will have one node, we'll call this **call openai** node and this will call the LLM. 
We'll then have a conditional edge which will check for the existence of an action to take so we will call this **exists_action**.
And then we'll have another node which actually executes the action. We'll call this **take_action**.

And the state that we'll track is pretty simple, it's just this list of messages to which we add over time.

```python
class AgentState(TypedDict):
    messages: Annotated[list[AnyMessage], operator.add]
```

Now let's dive into the code.
