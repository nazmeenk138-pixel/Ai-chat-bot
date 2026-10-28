
# TASK 1: STUDYSPHERE AI CHATBOT
# Your Smart Learning Assistant
# Python + Gradio | No API key required

%pip -q install gradio

import re
import random
import gradio as gr

# 1. Intent patterns and responses
INTENTS = {
    "greeting": {
        "patterns": [r"\b(hi|hello|hey|good morning|good evening)\b"],
        "responses": [
            "Hello! Welcome to StudySphere AI. How can I help you?",
            "Hi there! What would you like to learn today?"
        ]
    },
    "goodbye": {
        "patterns": [r"\b(bye|goodbye|see you)\b"],
        "responses": ["Goodbye! Keep learning and have a great day."]
    },
    "thanks": {
        "patterns": [r"\b(thanks|thank you|thx)\b"],
        "responses": ["You're welcome! Happy to help."]
    },
    "about": {
        "patterns": [r"\b(who are you|what are you|about you)\b"],
        "responses": [
            "I'm StudySphere AI, your smart learning assistant."
        ]
    },
    "help": {
        "patterns": [r"\b(help|what can you do|your features|options)\b"],
        "responses": [
            "I can help with study tips, exams, business, commerce, "
            "artificial intelligence, motivation, and general support."
        ]
    },
    "study": {
        "patterns": [r"\b(study tips|how to study|study better|concentrate)\b"],
        "responses": [
            "Study in focused sessions, take short breaks, remove "
            "distractions, and revise your notes regularly."
        ]
    },
    "exams": {
        "patterns": [r"\b(exam|exams|revision|prepare for test|test preparation)\b"],
        "responses": [
            "Create a timetable, revise important topics, practise "
            "past questions, and get enough sleep before your exam."
        ]
    },
    "business": {
        "patterns": [r"\b(business|entrepreneurship|entrepreneur|startup)\b"],
        "responses": [
            "A business provides goods or services to meet customer "
            "needs, usually to earn a profit."
        ]
    },
    "commerce": {
        "patterns": [r"\b(commerce|accounting|balance sheet|journal entry)\b"],
        "responses": [
            "Commerce includes trade and its supporting activities. "
            "Accounting records and summarises financial transactions."
        ]
    },
    "ai": {
        "patterns": [r"\b(ai|artificial intelligence|machine learning|chatbot)\b"],
        "responses": [
            "Artificial intelligence enables computers to perform "
            "tasks that usually require human intelligence."
        ]
    },
    "motivation": {
        "patterns": [r"\b(motivation|motivate|inspiration|give up|confidence)\b"],
        "responses": [
            "Believe in your progress. Small, consistent efforts "
            "can help you achieve your goals."
        ]
    },
    "support": {
        "patterns": [r"\b(sad|stressed|upset|worried|lonely|anxious)\b"],
        "responses": [
            "I'm sorry you're feeling this way. Take a gentle pause. "
            "If you feel comfortable, tell me what's troubling you."
        ]
    }
}

FOLLOW_UP = re.compile(
    r"\b(tell me more|explain more|more details|continue|why|how so)\b",
    re.I
)


# 2. Detect the user's intent
def detect_intent(message):
    text = message.lower().strip()

    for intent, data in INTENTS.items():
        if any(re.search(p, text) for p in data["patterns"]):
            return intent

    return "fallback"


# 3. Generate a response using conversation history
def chatbot(message, history):
    if not message or not message.strip():
        return "Please type a message."

    history = history or []

    if FOLLOW_UP.search(message):
        previous_user = next(
            (
                item.get("content", "")
                for item in reversed(history)
                if isinstance(item, dict)
                and item.get("role") == "user"
            ),
            None
        )

        if previous_user:
            topic = detect_intent(previous_user)

            if topic != "fallback":
                answer = random.choice(INTENTS[topic]["responses"])
                return (
                    f"Let's continue our discussion about: {previous_user}\n\n"
                    f"{answer}\n\nWhat else would you like to know?"
                )

        return "Of course! Which topic would you like me to explain?"

    intent = detect_intent(message)

    if intent == "fallback":
        return (
            "I'm not sure I understood that. Try asking about study tips, "
            "exams, business, commerce, AI, or motivation. "
            "Type 'help' to see my supported topics."
        )

    return random.choice(INTENTS[intent]["responses"])


# 4. Create the chat interface
demo = gr.ChatInterface(
    fn=chatbot,
    title="StudySphere AI - Your Smart Learning Assistant",
    description=(
        "An interactive learning chatbot for study tips, exam preparation, "
        "business, commerce, AI, and motivation."
    ),
    examples=[
        "Hello",
        "Give me study tips",
        "How do I prepare for exams?",
        "What is artificial intelligence?",
        "Explain commerce",
        "I need motivational"
    ]
)

# 5. Launch the chatbot
demo.launch(share=True)
