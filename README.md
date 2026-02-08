# 🧭 Dwilar Voice Real Estate Agent [Project ID: P-12334]

An AI-powered voice agent that helps users find their perfect property through natural conversation, leveraging LiveKit for real-time communication, OpenAI for intelligence, and Pinecone for semantic property search — with bilingual support for English and Japanese.

---

## 📚 Table of Contents

- [About](#-about)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Installation](#%EF%B8%8F-installation)
- [Usage](#-usage)
- [Configuration](#-configuration)
- [Screenshots](#-screenshots)
- [API Documentation](#-api-documentation)
- [Contact](#-contact)
- [Acknowledgements](#-acknowledgements)

---

## 🧩 About

Finding the right property can be overwhelming — browsing countless listings, filtering by location, price, and size, and still not being sure. This project solves that by providing an **AI voice agent** that acts as a personal real estate assistant from Dwilar Company.

Users simply **speak** their preferences (desired location, budget, number of bedrooms), and the agent performs a **semantic vector search** across a curated property database to recommend the top matches. It handles the full conversation flow — from greeting and consent collection, through property presentation and selection, all the way to capturing contact information for follow-up viewings.

Key goals:
- Deliver a hands-free, conversational property discovery experience
- Support bilingual interaction (English & Japanese)
- Enable seamless frontend integration via LiveKit RPC for UI updates (property cards, contact forms)

---

## ✨ Features

- **Real-time Voice Conversation** – Bidirectional audio powered by LiveKit with noise cancellation and voice activity detection
- **Bilingual Support (EN/JA)** – Automatic language switching with locale-specific TTS voices
- **Semantic Property Search** – Vector-based search via Pinecone using OpenAI embeddings to find the best-matching listings
- **Guided Conversation Flow** – Structured agent dialogue: greeting → consent → requirements → search → details → contact capture → goodbye
- **Frontend RPC Integration** – Pushes property results, contact forms, and UI state to the client via LiveKit RPC methods
- **Automated Lead Capture** – Collects email and phone through voice or frontend form and submits for follow-up
- **Configurable AI Personality** – Customizable system prompt controls tone, language, and conversation rules

---

## 🧠 Tech Stack

| Category    | Technologies                                                    |
| ----------- | --------------------------------------------------------------- |
| **Language**    | Python 3.8+                                                 |
| **Voice/RTC**   | LiveKit Agents SDK, LiveKit RTC                             |
| **AI / LLM**    | OpenAI GPT-4o-mini, OpenAI Embeddings (`text-embedding-3-small`) |
| **Vector DB**   | Pinecone (Serverless, cosine similarity)                    |
| **STT**         | Deepgram Nova-2 (multi-language)                            |
| **TTS**         | Google Cloud Text-to-Speech, AWS Polly (Japanese fallback), Deepgram TTS, ElevenLabs |
| **VAD**         | Silero Voice Activity Detection                             |
| **Utilities**   | python-dotenv, tqdm, asyncio                                |

---

## ⚙️ Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/dwilar-voice-agent-v1.git

# Navigate to the project directory
cd dwilar-voice-agent-v1

# (Recommended) Create and activate a virtual environment
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Prerequisites

- Python 3.8+
- An [OpenAI](https://platform.openai.com/) API key
- A [Pinecone](https://www.pinecone.io/) API key and environment
- A [LiveKit Cloud](https://livekit.io/) account (URL, API key, API secret)
- A [Deepgram](https://deepgram.com/) API key
- A [Google Cloud](https://cloud.google.com/) service account JSON for TTS

---

## 🚀 Usage

### 1. Index property data into Pinecone

```bash
python vectordb.py
```

This reads `data.json`, generates embeddings for each property listing, and upserts them into your Pinecone index.

### 2. Start the voice agent

```bash
python agent.py
```

The agent connects to your LiveKit room and waits for participants.

### 3. (Optional) Test vector search standalone

```bash
python vectorsearch.py
```

Enter location, price, and bedroom count interactively to preview search results.

### Conversation Flow

1. **Greeting** – Agent introduces itself as a Dwilar Company real estate agent
2. **Consent** – Asks user permission to collect information
3. **Requirements** – Collects desired location, price range (USD), and number of bedrooms one at a time
4. **Search** – Calls `search_real_estate()` to find the top 3 matching properties via Pinecone
5. **Presentation** – Describes each property in a concise, natural way
6. **Selection** – Lets the user pick a property or search again
7. **Details** – Provides more information on the chosen property
8. **Contact Capture** – Shows a contact form and collects email & phone number
9. **Goodbye** – Politely closes the conversation with a follow-up promise

---

## 🧾 Configuration

### Environment Variables

Create a `.env` file in the project root (see `.env.example`):

```env
DEEPGRAM_API_KEY=your-deepgram-key

OPENAI_API_KEY=your-openai-api-key

LIVEKIT_URL=wss://your-livekit-url
LIVEKIT_API_KEY=your-livekit-api-key
LIVEKIT_API_SECRET=your-livekit-api-secret

PINECONE_API_KEY=your-pinecone-api-key
PINECONE_ENV=your-pinecone-env
PINECONE_INDEX_NAME=your-pinecone-index-name

GOOGLE_APPLICATION_CREDENTIALS=./google-application-credentials.json
```

### Audio Pipeline Settings

| Component            | Setting                          |
| -------------------- | -------------------------------- |
| **STT**              | Deepgram Nova-2, multi-language  |
| **TTS (English)**    | Deepgram `aura-asteria-en`       |
| **TTS (Japanese)**   | AWS Polly `Mizuki` (standard)    |
| **VAD**              | Silero, activation threshold 0.7 |
| **Noise Cancellation** | LiveKit BVC                    |
| **LLM**              | GPT-4o-mini, temperature 0.3    |

### Project Structure

```
dwilar-voice-agent-v1/
├── agent.py            # Main AI voice agent (full-featured with RPC & contact forms)
├── agent1.py           # Simplified/alternative agent implementation
├── vectordb.py         # Pinecone index creation & property data upsert
├── vectorsearch.py     # Standalone vector search testing utility
├── prompt.py           # System prompt defining agent personality & conversation rules
├── data.json           # Property listings dataset (JSON)
├── requirements.txt    # Python dependencies
├── .env.example        # Environment variable template
└── .gitignore          # Git ignore rules
```

---

## 🖼 Screenshots

> _Screenshots and demo GIFs of the voice agent in action will be added here._

<!-- Example:
![Voice Agent Demo](docs/demo.gif)
-->

---

## 📜 API Documentation

### LiveKit RPC Methods

The agent exposes the following RPC methods for frontend communication:

| Method              | Direction       | Description                                      |
| ------------------- | --------------- | ------------------------------------------------ |
| `initData`          | Agent → Client  | Sends top property search results to the frontend |
| `showContactForm`   | Agent → Client  | Triggers the contact form UI on the client        |
| `submitContactInfo` | Agent → Client  | Sends collected contact details and hides form    |
| `getContactInfo`    | Agent → Client  | Retrieves stored contact information              |

### Agent Function Tools

| Tool                       | Parameters                          | Description                                    |
| -------------------------- | ----------------------------------- | ---------------------------------------------- |
| `search_real_estate()`     | `location`, `price`, `bedrooms`     | Semantic vector search for matching properties  |
| `show_contact_form()`      | —                                   | Display contact collection form via RPC         |
| `submit_contact_info()`    | `email`, `phone`                    | Submit captured lead contact information        |
| `get_language()`           | —                                   | Returns current language code (`en` / `ja`)     |
| `initial_greeting()`       | —                                   | Returns the agent's introduction message        |

### Property Data Schema

```json
{
  "title": "Modern 3BR House",
  "imgs": ["https://..."],
  "videos": [],
  "floor_plan": ["https://..."],
  "property_detail": {
    "PROPERTY ID": "ABC123",
    "PRICE": "$450,000",
    "BEDROOMS": "3",
    "PROPERTY TYPE": "Single Family Homes",
    "TOTAL SQFT": "2,500",
    "STATUS": "Available"
  },
  "description_detail": {
    "Address": "Tokyo, Japan",
    "Structure": "Wooden, 2-story",
    "Year built": "2020"
  }
}
```

---

## 📬 Contact

- **Author:** Dwilar Company
- **GitHub:** [Vector99dev](https://github.com/yourgithub)
- **Email:** challengemode45@gmail.com
- **Website:** [Not deployed](https://dwilar.com)

---

## 🌟 Acknowledgements

- [LiveKit](https://livekit.io/) — Real-time communication infrastructure
- [OpenAI](https://openai.com/) — GPT-4o-mini LLM and text embeddings
- [Pinecone](https://www.pinecone.io/) — Serverless vector database
- [Deepgram](https://deepgram.com/) — Speech-to-text and text-to-speech
- [Google Cloud](https://cloud.google.com/) — Text-to-speech services
- [Silero](https://github.com/snakers4/silero-vad) — Voice Activity Detection model
- [ElevenLabs](https://elevenlabs.io/) — High-quality voice synthesis