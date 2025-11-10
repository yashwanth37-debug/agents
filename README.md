🧠 LiveKit Filler Word Interrupt Handler Agent
Real-time conversational AI with intelligent filler interruption filtering

Author: Guttula Viswa Venkata Yashwanth

🔍 Overview

This repository implements an AI-powered LiveKit voice agent that intelligently distinguishes between filler-based interruptions (e.g., “uh”, “umm”, “hmm”, “haan”) and genuine user commands (e.g., “wait”, “stop”, “no”).

The project extends the LiveKit Agents SDK to maintain smooth, natural dialogue by ignoring meaningless fillers while the agent is speaking, without modifying the core SDK.

🧩 Key Features

  •🎙️ Real-time interruption filtering
    Filters filler words only when the agent is speaking.

  •⚡ True interruption detection
    Immediately stops speech when real user input (like “stop”) is detected.

  •🧠 Confidence-based handling
    Ignores low-confidence transcriptions that are likely background noise.

  •🔄 Dynamic filler list
    Add or remove filler words at runtime.

  •📊 Logging & analytics
    Tracks statistics for ignored vs. valid interruptions.
  
  
🧱 Project Structure

  📁 https://github.com/yashwanth37-debug/agents/tree/feature/livekit-interrupt-handler-GuttulaViswaVenkataYashwanth/
  │
  ├── filler_filter.py        # Core filtering logic for filler interruptions
  ├── interrupt_handler.py    # Main LiveKit agent integrating the filter
  └── README.md               # Project documentation

⚙️ How It Works

🧩 filler_filter.py

  Implements the FillerInterruptionFilter class that decides whether a user’s transcription should be ignored or treated as meaningful input.

  Logic Summary:
  1. If the agent is not speaking, all user speech is valid.

  2. If the agent is speaking:
     • Ignore input if it contains only filler words or has low confidence.
     • Accept input if it includes any non-filler or command words.

  Example Configuration:

    ignored_words = ['uh', 'umm', 'hmm', 'haan', 'achha']
    filter = FillerInterruptionFilter(ignored_words, confidence_threshold=0.65)

🧩 interrupt_handler.py
  
  Main Responsibilities:
   • Uses free-tier integrations:
     • silero.VAD → Voice Activity Detection
     • deepgram.STT → Speech-to-Text
     • groq.LLM → Text generation
     • cartesia.TTS → Speech synthesis
   • Tracks when the agent is speaking or silent.
   • Applies filler filtering on every transcription.
   • Logs filler vs. valid user speech and prints final stats on shutdown.
  
🚀 Running the Agent

1️⃣ Clone and Set Up

  git clone https://github.com/<your-username>/<your-repo-name>.git
  cd <your-repo-name>

2️⃣ Install Dependencies
  
  Make sure you have Python 3.10+ and uv installed.
  uv sync
  
3️⃣ Configure Environment
  
  Create a .env.local file with:
  
    LIVEKIT_API_KEY=lk_api_key_1234567890abcdef
    LIVEKIT_API_SECRET=lk_secret_key_abcdef1234567890
    LIVEKIT_URL=wss://your-livekit-server-url.livekit.cloud  
    
    DEEPGRAM_API_KEY=dg_api_key_abcdef1234567890abcdef
    GROQ_API_KEY=gsk_your_groq_api_key_abcdef1234567890
    CARTESIA_API_KEY=cartesia_api_key_abcdef1234567890abcdef
    
4️⃣ Run the Agent

  uv run python interrupt_handler.py dev
  
🧪 Example Behavior

  Scenario                  Agent Speaking     Input             Behavior
  ---------------------------------------------------------------------------
  “uh”, “umm”                     Yes          ✅                 Ignored
  “wait a sec”                    Yes          ✅                 Agent stops
  “umm okay stop”                 Yes          ✅                 Agent stops
  “umm”                           No           ❌                 Registered
  “hmm yeah” (low confidence)     Yes          ✅                 Ignored

🧰 Configuration Options

  Parameter              Description                                   Default
  -------------------------------------------------------------------------------
  ignored_words          List of filler words to ignore                 ['uh', 'umm', 'hmm', 'haan', ...]
  confidence_threshold   Minimum ASR confidence to accept as valid      0.65
   
     
  Dynamic methods available:

    filter.add_filler_word("arre")
    filter.remove_filler_word("umm")
    

🧠 Tech Stack

  Component                 Library / Provider
  --------------------------------------------------------
  Voice Activity Detection   Silero
  Speech-to-Text             Deepgram Nova-2
  LLM                        Groq Llama-3.1-8B-Instant
  Text-to-Speech             Cartesia
  Framework                  LiveKit Agents
  Language                   Python 3.10+

⚠️ Known Issues

  • No async buffering — overlapping speech may cause occasional timing delays.
  • Dynamic filler list updates currently available only through direct method calls.
  • Confidence threshold may vary slightly between different STT models.

🧩 When it could be improved

  If the pause feels too long (like >1s), you can smooth it by:
    • Adding a very short async resume timer after ignoring.
    • Example:
        import asyncio
        if should_ignore:
            asyncio.create_task(asyncio.sleep(0.2))
            resume_tts()  # if supported by your session
    • Or lower the confidence threshold slightly (e.g., 0.55) to make fillers ignored faster.

🏁 End Goal

  A natural, human-like conversational AI agent that:
    ✅ Speaks smoothly without awkward cutoffs.
    ✅ Ignores meaningless filler interruptions.
    ✅ Responds instantly to genuine user intent.
    
    
🧩 Branch

  feature/livekit-interrupt-handler-GuttulaViswaVenkataYashwanth
