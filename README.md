AURA (Agentic Ultimate Resource Assistant)
An Autonomous, Persistent, Multi-Phase Agentic AI Orchestrator
AURA is an enterprise-grade autonomous agent built on n8n, Google Gemini, MongoDB Atlas, and Telegram. Unlike traditional linear automation workflows, AURA functions as a persistent cognitive system capable of dynamic reasoning, adaptive tool selection, long-term memory retrieval across sessions, and safe Human-In-The-Loop (HITL) execution.
🏗️ Architecture Overview
AURA operates on a 4-phase modular agentic framework designed for resilience, adaptability, and state persistence:
+-----------------------------------------------------------------------------------+
|                               PHASE 1: CONVERSATIONAL BRAIN                      |
|  [ Telegram Trigger ] ---> [ n8n Main Orchestrator ] <---> [ Gemini Chat Model ]   |
+-----------------------------------------------------------------------------------+
                                        |
                                        v
+-----------------------------------------------------------------------------------+
|                             PHASE 2: AGENTIC REASONING                            |
|  Dynamic Tool Selection & Execution Loop:                                         |
|  ├── LinkedIn Search Tool (Apify)                                                 |
|  ├── Lead Qualification Engine                                                    |
|  ├── User Profile Memory Tool                                                     |
|  └── Outbound Message Drafting Tool                                              |
+-----------------------------------------------------------------------------------+
                                        |
                                        v
+-----------------------------------------------------------------------------------+
|                        PHASE 3: HUMAN-IN-THE-LOOP (HITL)                          |
|  Draft Creation ---> [ Interactive Telegram Webhook ] ---> [ Gmail Executor ]      |
|                      (Approve / Reject / Revise)                                  |
+-----------------------------------------------------------------------------------+
                                        |
                                        v
+-----------------------------------------------------------------------------------+
|                       PHASE 4: PERSISTENT MEMORY & STATE                          |
|  [ MongoDB Atlas ] <---> Mapped via `{{ $json.message.chat.id }}`                 |
|  Stores: Session Logs | Execution Context | User Preferences                     |
+-----------------------------------------------------------------------------------+


✨ Key Features & Capabilities
🧠 Dynamic Tool Orchestration: Gemini automatically selects tools (Apify LinkedIn Scraper, Lead Scorer, User Profiler) based on unstructured natural language intent.
💾 Persistent Session Memory: Utilizes MongoDB Atlas mapped directly to Telegram chat.id to maintain full conversation context, user preferences, and goal tracking across multiple turns.
🛡️ Human-In-The-Loop (HITL) Safety: Critical actions (e.g., sending emails via Gmail API) generate interactive Telegram inline keyboard webhooks with Approve, Reject, and Revise options before execution.
⚙️ Network & Security Hardened: Configured with MongoDB Atlas IP Whitelisting (0.0.0.0/0 / dedicated n8n node IP) and SSL/TLS v1.3 handshake verification for reliable cloud connectivity.
🧰 Tech Stack
Orchestrator: n8n Cloud (Workflow & Node Engine)
Core Model: Google Gemini Flash API (ChatModel)
Long-Term Memory: MongoDB Atlas (MongoDB Chat Memory node)
Primary Interface: Telegram Bot API (Telegram Trigger & SendMessage)
Data Scraper / Tools: Apify (LinkedIn Lead Extraction) & Custom JS/Python Code Nodes
Execution Engines: Gmail API, Custom Webhook Callbacks
🚀 Setup & Installation Guide
Prerequisites
n8n Cloud or self-hosted instance.
Google Gemini API Key via Google AI Studio.
MongoDB Atlas Cluster (Free tier or higher).
Telegram Bot Token obtained from @BotFather.
Apify API Token for LinkedIn search tools.
1. MongoDB Atlas Configuration
Navigate to Network Access in MongoDB Atlas and add 0.0.0.0/0 (or your n8n IP address) to the IP Access List.
Retrieve your connection string in the following format:
mongodb+srv://:@cluster0.xxx.mongodb.net/?retryWrites=true&w=majority


2. n8n Node Configuration
Telegram Trigger: Connect your BotFather token. Set Event to message.
MongoDB Chat Memory:
Set Session ID to Define below.
Enable Expression (fx) on Key:
{{ $json.message.chat.id }}


Set Database Name to memory and Collection Name to n8n_chat_histories.
Lead Research Agent:
Set Prompt (User Message) to Expression (fx):
{{ $json.message.text }}


Paste the System Message prompt into the Agent config.
3. Final Execution Node
Connect a Telegram Node to the output of Lead Research Agent:
Chat ID: {{ $('Telegram Trigger').item.json.message.from.id }}
Text: {{ $json.output }}
📝 License
Distributed under the MIT License. See LICENSE for more information.
