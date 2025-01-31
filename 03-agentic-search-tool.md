# Agentic Search
Before discussing what **Agentic Search** is, let's first discuss how an Agent might use it.
In **Zero Shot Learning** an Agent would receive a prompt and will produce an answer based on it's static weights of the model.

As powerful as it's proven to be, there are many limitations to this process.
- First, the data on this is dynamic. So we couldn't for instance ask about the scores from the game last night. 
- Secondly, in many usecases we would want to know the sources of the information provided in the result. 
    - This can reduce the hellucinations and smooth the friction of this human-computer interaction.

<img width="462" alt="Screenshot 2025-01-31 at 3 00 04 PM" src="https://github.com/user-attachments/assets/3f7f2432-e305-436b-a91a-7078d7724af4" />

Looking at above picture, we can see the prompt is received by the agent, which then decides to call the search tool. Then the information found is returned to the agent.
Looking at above picture, we can see the prompt is received by the agent, which then decides to call the search tool. Then the information found is returned to the agent.

## Inside a Search Tool
This is an example of a very basic search tool implementation. Let's go over it step by step.

<img width="820" alt="Screenshot 2025-01-31 at 3 20 11 PM" src="https://github.com/user-attachments/assets/ad861192-8cac-4d34-9ca3-d33ce38d776e" />

If the agent decides it will send the query to the search tool. The first step would work on understanding the question and divide it to sub-questions if needed. This is an important step because it can handle complex queries. 

The for each subquery, the search tool will have to find the best source. Choosing from multiple integrations. For example, if an agent would ask "How is the weather in San Francisco?" 
The search tool should use the weather API for the best results. The Job doesn't end with finding the correct source, the search tool would then have to extract only the relevant information to the subquery.

A basic implementation of this can be achieved through a process of chunking the source, and run a quick vector search to retrieve top-K chunks. After retrieving the data from its source, the search tool would then score the results and filter out the less relevant information.
