# Memory Enhanced ChatBot

This project demonstrates how to build a chatbot that can **remember past conversations** and use those memories to generate more contextual, personalized responses. It combines a vector database, custom memory logic, and Euri’s LLM/embedding APIs.

## Project Overview

This chatbot goes beyond simple one-turn Q&A:

- Remembers **past interactions** and stores them as vector embeddings.
- Uses a **vector database (FAISS / Facebook AI Similarity Search)** to retrieve relevant past memories.
- Uses **Euri API** for:
  - Real-time chat completion
  - Embedding generation (via a small GPT model)
- Implements a **RAG-style (Retrieval-Augmented Generation)** flow without LangChain (LangChain was initially used but later removed due to memory issues).

## System Architecture

High-level flow:

1. **Chat Interface (Streamlit UI)**
   - User types a message in a chat window.
   - API testing and user identity settings.
   - Option to add custom memory.

2. **RAG-Based Processing**
   - User input is converted into embeddings using Euri embeddings.
   - Embeddings are stored in a **FAISS** vector database.
   - On each query:
     - Retrieve **relevant memories** from the vector store.
     - Fetch **recent conversation history**.
     - Combine:
       - user input
       - history
       - retrieved memories
       into a **single prompt**.

3. **Memory Types**
   - **Contextual memory**: recent chat turns.
   - **Long-term memory**: stored embeddings in the vector database.

4. **Response Generation**
   - Final prompt is sent to the **Euri chat completion model**.
   - Response is:
     - Displayed in the UI
     - Appended to history
     - Optionally stored as memory

## Code Structure

The codebase is organized into several classes, each with a clear responsibility.

### 1. `Setting` Class

- Loads:
  - API keys (from `.env`)
  - Embedding model configuration
  - Chat model configuration
- Defines core parameters:
  - Maximum tokens
  - Temperature
  - Number of relevant memories to retrieve
  - History limit (how many past messages to include)

### 2. `EuriEmbedding` Class

- Wrapper around the **Euri embedding API**.
- Responsibilities:
  - Convert text into embeddings (vectors).
  - Provide an easy interface for the memory store to request embeddings.

### 3. `EuriChat` Class

- Wrapper around the **Euri chat completion API**.
- Responsibilities:
  - Send prompts to the chat model.
  - Receive and return generated responses.

### 4. `MemoryVectorStore` Class

- Manages vector-based memory using **FAISS**.
- Responsibilities:
  - Add new memory entries (user messages, facts, summaries).
  - Search for **top-k relevant memories** based on a query embedding.
  - Clear memory (reset the vector store) when needed.

### 5. `ConversationalMemory` Class

- Stores **conversation history** in a JSON file.
- Responsibilities:
  - Append user and assistant messages.
  - Load and format previous turns according to:
    - History limit
    - Relevance rules (if applied in the code)

### 6. `MainChat` Class

- The orchestrator of the chatbot flow.
- Responsibilities:
  - Add user messages to history.
  - Retrieve and format conversation history.
  - Perform memory search in the vector store.
  - Build the final prompt:
    - User input
    + History
    + Retrieved memories
  - Call `EuriChat` to generate the response.
  - Return the response to the Streamlit UI.

## Key Functionalities

- **Adding User Messages**
  - Stores each user message in conversation history.
  - Optionally embeds and stores messages in the vector database.

- **Retrieving History**
  - Fetches formatted conversation history.
  - Limits the number of past messages based on configuration.

- **Performing Search**
  - Converts user query to embeddings.
  - Retrieves **top relevant memories** from FAISS.

- **Formatting Memory and Prompt**
  - Combines:
    - User input
    - Conversation history
    - Relevant long-term memories
  - Produces a structured prompt for the LLM.

- **Generating Response**
  - Uses Euri’s chat completion model.
  - Response is:
    - Displayed in the UI
    - Saved into history
    - Optionally stored as new memory

## Streamlit UI

The project includes a **Streamlit-based user interface**:

- **Header / Title**
- **API Testing Section**
  - Check if Euri API key and endpoints are working correctly.
- **User Identity Settings**
  - Define user identity/profile used in prompts.
- **Chat Window**
  - Type messages and receive responses from the chatbot.
- **Custom Memory Input**
  - Add custom memory entries (facts, preferences, context).
  - These are embedded and stored in the vector database for future use.

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Anurag0798/Memory-Enhanced-ChatBot.git

cd Memory-Enhanced-ChatBot
```

### 2. Set Up Environment

Create and activate a virtual environment (optional but recommended):

```bash
python -m venv .venv

# On Linux / macOS
source .venv/bin/activate

# On Windows
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

### 3. Configure `.env`

Modify the `.env` file in the project root and add:

```env
EURI_API_KEY=your_euri_api_key_here

USER_IDENTITY=your_identity_here
```

> **Note:** Check the source code (especially the `Setting` class) for the exact variable names and any additional required settings.

### 4. Reset Data (Optional but Recommended)

To start with a clean state:

- Delete the `data` folder (if it exists) to reset:
  - The FAISS vector store
  - Conversation history JSON

### 5. Run the Streamlit App

Run the Streamlit app (replace the filename with the actual main script in the repo, e.g. `app.py`):

```bash
streamlit run app.py
```

Open the given local URL in your browser.

## How to Use the ChatBot

1. Open the Streamlit UI in your browser.
2. Optionally test the Euri API connection (if a test button/section exists).
3. Set your **user identity** if supported.
4. Start chatting in the chat window.
5. Add **custom memories** (e.g., “I like Python”, “My project is about finance”) through the UI.
6. Observe how the bot uses these memories in later responses to give more personalized, context-aware answers.

## Main Takeaways

- The chatbot leverages a **vector database** to store and retrieve relevant memories.
- It uses a **RAG-based approach**, combining:
  - User input
  - Conversation history
  - Retrieved long-term memories
  into a single, rich prompt for the LLM.
- The code is:
  - **Well-documented**
  - **Organized into modular classes**
  - Easy to extend (e.g., swap models, adjust memory logic)
- It demonstrates how to build a chatbot that:
  - Remembers past conversations
  - Provides more informed and personalized responses over time.

## Practical Steps Summary

- Download/clone the project.
- Configure the `.env` file with:
  - Euri API key
  - User identity
- Delete the `data` folder to start with a fresh database and history.
- Run the Streamlit app with:
  - `streamlit run app.py` (or the main file).
- Experiment with:
  - Adding custom memory
  - Chatting with the bot
  - Reviewing code class-by-class to understand the full flow.

## Contributing

Contributions are welcome!

1. Fork the repository.
2. Create a new feature branch.
3. Make your changes.
4. Open a Pull Request with a clear description of your modifications.

## License

This project is open-source. See the `LICENSE` file in the repository for full details.