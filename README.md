🧠 RAG Pipeline & Chatbot — Project Notes
📘 Overview

RAG (Retrieval-Augmented Generation) combines retrieval-based search with generative AI to produce informed, accurate answers.
The pipeline works by retrieving relevant information from a vector database and using an LLM to generate a coherent response.

    Retrieval → Finds relevant context from stored documents.

    Augmentation → Injects that context into the LLM query.

    Generation → Produces an answer that’s grounded in the retrieved data.

💾 Understanding Vector Databases

    A vector database stores text as numerical embeddings (vectors).

    Each vector represents the semantic meaning of text, not just keywords.

    Similar meanings = vectors placed closer in multi-dimensional space.

Example:

    “Wolf”, “Dog”, and “Cat” → clustered together (animals).

    “Apple” and “Banana” → clustered together (fruits).

When a query like “kitten” is asked:

    It’s converted to a vector using the same embedding model.

    The system retrieves nearby vectors (similar meanings).

    Those are used as context for the answer.

🧩 Data Preparation Pipeline

Steps:

    Split documents into smaller chunks (since large files can’t be embedded whole).

    Embed text using a model (e.g., OpenAI embeddings: text-embedding-3-small).

    Store embeddings in a vector database (Pinecone).

Chunks are organized by context (e.g., company data, marketing data, etc.), keeping semantic structure intact.
⚙️ Workflow Setup (N8N)

1. Trigger Step (Google Drive Integration)

    Add trigger: “On changes in a specific folder.”

    Connect Google Drive via OAuth credentials:

        Create project in Google Cloud Console.

        Enable Drive API.

        Configure OAuth Consent Screen (External, test mode).

        Add redirect URI from N8N.

        Retrieve and input Client ID and Client Secret into N8N.

Result: Workflow starts when a new file is added to your chosen Google Drive folder (e.g., /FAQ/).

2. Download File Node

    Use Google Drive node → Download File by File ID (dynamic reference).

    Output: File’s binary data (the actual document).

3. Setup Pinecone Vector Store

    Go to pinecone.io.

    Create an index (e.g., sample).

        Use embeddings model: text-embedding-3-small.

        Choose serverless + AWS region.

    Create an API Key and connect it in N8N.

In N8N:

    Add Pinecone node → “Add documents to vector store”.

    Define:

        Index: sample

        Namespace: FAQ

        Add Embeddings model + Document Loader + Text Splitter

        Split chunks (e.g., size = 1000 characters, overlap = 0)

You’ve now built the base RAG data ingestion pipeline!
🤖 Building the Chatbot (RAG Agent)

1. Create an AI Agent Node

    Add an AI agent to the workflow.

    Connect a Chat Trigger so messages entered trigger the agent.

2. Add LLM Model

    Use OpenRouter or OpenAI API:

        Sign up at openrouter.ai

        Connect via API Key.

        Choose from models (e.g., Claude 3.5 Sonnet or GPT-4).

3. Connect the Vector Store (Knowledge Base)

    Add Pinecone Vector Store as a tool under the AI agent:

        Name: knowledgebase

        Description: “Accesses policy and FAQ database.”

        Index: sample

        Namespace: FAQ

        Embedding Model: text-embedding-3-small

💬 Testing the Chatbot

Ask:

    “What is our warranty policy?”

    The chatbot queries Pinecone using vector search.

    Retrieves nearest chunks (context).

    LLM generates an answer incorporating that context.

Output Example:

    Based on our policy database: 1-year standard coverage, email for claims, exclusions for misuse or water damage.
