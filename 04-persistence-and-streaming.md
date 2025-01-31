When building Agents, they are often running on longer running tasks. For these types of tasks there are two really important concepts:
- Persistence
- Streaming

## Persistence
Lets you keep around the state of an Agent at a particular point in time. This can let you go back to that state and resume in that state in future interactions. This is really important for long running applications.

## Streaming
Likewise with streaming, you can emit a list of signals of what's going on at that exact moment. So for long running applications, you know exactly what the agent is doing.

