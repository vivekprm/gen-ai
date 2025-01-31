# Agentica Search
Before discussing what **Agentic Search** is, let's first discuss how an Agent might use it.
In **Zero Shot Learning** an Agent would receive a prompt and will produce an answer based on it's static weights of the model.

As powerful as it's proven to be, there are many limitations to this process.
- First, the data on this is dynamic. So we couldn't for instance ask about the scores from the game last night. 
- Secondly, in many usecases we would want to know the sources of the information provided in the result. 
    - This can reduce the hellucinations and smooth the friction of this human-computer interaction.

Looking at above picture, we can see the prompt is received by the agent, which then decides to call the search tool. Then the information found is returned to the agent.