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

May be biggest part of this application that most code that we wrote, was this function that would loop and that's what is represented by all these arrows.

<img width="889" alt="Screenshot 2025-01-30 at 11 44 23 AM" src="https://github.com/user-attachments/assets/dc54f1f0-5cc8-4aad-967a-972e8c3dec99" />

