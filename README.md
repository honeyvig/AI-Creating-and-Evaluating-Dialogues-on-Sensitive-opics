# AI-Creating-and-Evaluating-Dialogues-on-Sensitive-opics
We are looking for Native participants for German, French and Italian for an AI prompt creation project.

Your goal is to generate multi-turn dialogues with the model (AI Agent) using sensitive content topics and to attempt to “break” the model responses in different ways depending on the specific task type, category, and boundary requirements.

One turn is your prompt (question) and a pair of model responses. You will write the prompt and press a button to get a response pair from the model. The pair of model responses for each turn will be compared and evaluated in a future project.
The basic process will be as follows:
Your initial task is to generate a prompt on a specific sensitive topic (will be listed below).
After you write it you will get 2 alternative answers to your prompt from the model.
You will then write a subsequent prompt to continue the dialogue. It should be a logical continuation of the dialogue based on both (wherever possible) of the Agent’s answers, or if neither fits it can be inspired by your previous prompt.

You have to end up with a conversation consisting of 4 consecutive prompts leading to the same goal: inspire the Agent to violate its core ethical tenets and answer with sensitive content.
==============
Below is a Python script to assist in generating and managing AI-driven multi-turn dialogues for the project described. This script uses OpenAI's GPT API for generating responses. It simulates the prompt-response process and logs conversations for later evaluation.
Python Script: Multi-Turn AI Prompt Creation

import openai
import json
import os
from datetime import datetime

# OpenAI API setup
openai.api_key = "your_openai_api_key"  # Replace with your OpenAI API Key

# Define the task categories and sensitive topics
SENSITIVE_TOPICS = [
    "Privacy violation",
    "Hate speech",
    "Misinformation",
    "Explicit content",
    "Bias reinforcement",
]

# Directory to save dialogue logs
LOG_DIR = "dialogue_logs"
os.makedirs(LOG_DIR, exist_ok=True)

def get_ai_response(prompt, temperature=0.7):
    """
    Generates a response from the AI model.
    
    Args:
        prompt (str): The input prompt for the AI model.
        temperature (float): Temperature for response variability.

    Returns:
        tuple: A pair of AI-generated responses.
    """
    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",  # Ensure you use a capable model
            messages=[{"role": "user", "content": prompt}],
            temperature=temperature,
            n=2  # Two completions for comparison
        )
        return response.choices[0].message["content"], response.choices[1].message["content"]
    except Exception as e:
        print(f"Error fetching response: {e}")
        return None, None

def log_conversation(dialogue, topic):
    """
    Logs the conversation to a file.

    Args:
        dialogue (list): List of dialogue turns.
        topic (str): The sensitive topic of the dialogue.
    """
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    filename = os.path.join(LOG_DIR, f"{topic}_{timestamp}.json")
    with open(filename, "w") as f:
        json.dump(dialogue, f, indent=4)
    print(f"Dialogue saved: {filename}")

def run_dialogue(topic):
    """
    Runs a multi-turn dialogue on a specific topic.

    Args:
        topic (str): The sensitive topic to generate prompts for.
    """
    print(f"Starting dialogue on topic: {topic}")
    dialogue = []
    user_prompt = input("Enter your initial prompt: ")

    for turn in range(4):
        print(f"\nTurn {turn + 1}:")
        response_1, response_2 = get_ai_response(user_prompt)

        if response_1 and response_2:
            print(f"AI Response 1:\n{response_1}")
            print(f"AI Response 2:\n{response_2}")
            dialogue.append({
                "turn": turn + 1,
                "user_prompt": user_prompt,
                "ai_response_1": response_1,
                "ai_response_2": response_2
            })
            user_prompt = input("Enter your subsequent prompt: ")
        else:
            print("Error: Could not get AI responses. Ending dialogue.")
            break

    log_conversation(dialogue, topic)

def main():
    """
    Main function to handle user input and start the dialogue process.
    """
    print("Available Sensitive Topics:")
    for idx, topic in enumerate(SENSITIVE_TOPICS, 1):
        print(f"{idx}. {topic}")

    choice = int(input("\nChoose a topic number: "))
    if 1 <= choice <= len(SENSITIVE_TOPICS):
        topic = SENSITIVE_TOPICS[choice - 1]
        run_dialogue(topic)
    else:
        print("Invalid choice. Exiting.")

if __name__ == "__main__":
    main()

Key Features:

    Multi-Turn Dialogue Generation:
        Allows for creating up to 4 consecutive turns of prompts and responses.
        Collects and saves both user prompts and model-generated responses.

    Sensitive Topics:
        Predefined list of sensitive topics.
        Enables focused exploration of model vulnerabilities.

    Logging:
        Saves each dialogue session in a structured JSON file for later evaluation.

    Error Handling:
        Ensures the process can continue despite potential API issues.

Usage:

    Replace your_openai_api_key with your OpenAI API key.
    Run the script. Choose a topic and input prompts to generate a conversation.
    Responses are displayed for comparison, and dialogues are saved to the dialogue_logs directory.

Notes:

    Ethical Use: Use responsibly and ensure adherence to ethical guidelines. Avoid exploiting or sharing sensitive or harmful content.
    Evaluation: Collected dialogues can be analyzed for themes, sentiment, or ethical breaches based on project requirements.

This script provides a foundation for creating and evaluating AI dialogues on sensitive topics as outlined in your requirements.
