Let's breakdown what we did in lesson 1.

<img width="645" alt="Screenshot 2025-01-30 at 11 19 33 AM" src="https://github.com/user-attachments/assets/88a55282-5e1e-47a8-8548-961cdd6b4770" />

We had this user message that came in, we then had the system prompt and this was very long one. 
We then called an LLM with that. This outputted somthing like what's right there a thought and an action.
Based on that we then made a decision, we would either return or we would call a tool. and we put this on a big loop i.e. query funtion and we had these two tools that we would call "calualte" and "average_dog_weight".

If we call the tool we would get back an observation and then we would loop back in, to the prompt, and we would put that observation as a new message.
