# HealthBot

HealthBot is a patient education chatbot built with LangGraph. You give it a health topic, it searches the web for reliable info, summarizes it in plain language, and then quizzes you on it to check you understood. After grading your answer it asks if you want to learn about something else or stop.

This was built as a prototype for MediTech Solutions. The point is to help patients actually understand their conditions and treatments instead of leaving the clinic confused.

## How it works

The flow goes like this:

1. Ask the patient what they want to learn about
2. Search Tavily for medical info, focusing on sources like Mayo Clinic, CDC, NIH and WHO
3. Summarize the results in patient-friendly language (3-4 paragraphs)
4. Show the summary and let them read it
5. Wait until they say they're ready for the quiz
6. Generate one quiz question from the summary
7. Show the question and take their answer
8. Grade it (A-F) with feedback that cites parts of the summary
9. Show the grade
10. Ask if they want a new topic or to exit
11. Reset the state and loop, or end

It's a LangGraph state machine. Each step is its own node and they pass data through a shared state object (`HealthBotState`) that holds the messages, topic, search results, summary, quiz question, answer and grade.

## Setup

You need an OpenAI key and a Tavily key. Make a file called `config.env` next to the notebook:

```
OPENAI_API_KEY="sk-..."
TAVILY_API_KEY="tvly-..."
```

Tavily is free for the first 1000 requests, sign up at app.tavily.com.

Install the dependencies with uv:

```
uv venv --python 3.11.13
.\.venv\Scripts\Activate
uv add -r requirements.txt
```

Then open `healthbot.ipynb` and run the cells top to bottom. The last cell starts a session. Type your answers into the input boxes when it asks.

## Notes

- The summary, quiz and grading prompts all tell the model to only use the search results, not its own knowledge.
- When you start a new topic the state is reset so nothing from the last topic carries over. The messages list uses the `add_messages` reducer, so the reset uses `RemoveMessage` to actually clear it rather than returning an empty list (which wouldn't work).
- It's a prototype, not medical advice.
