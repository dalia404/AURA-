AURA — Agentic Ultimate Resource Assistant

An Autonomous, Persistent, Multi-Phase AI Agent Orchestration System

AURA is a multi-phase agentic AI system engineered using n8n, Google Gemini, MongoDB Atlas, and Telegram.

Unlike conventional linear automation workflows, AURA combines conversational reasoning, dynamic tool orchestration, persistent memory, specialized task execution, and Human-in-the-Loop (HITL) safeguards.

The system enables users to interact with an AI agent through natural language, delegate research and qualification tasks, maintain context across sessions, and review proposed external actions before execution.

AURA is designed around four integrated architectural phases, transforming conversational intent into structured, user-approved actions.

⸻

Table of Contents

* Overview
* Key Features
* System Architecture
* Four-Phase Architecture
* Human-in-the-Loop Execution
* Technology Stack
* Data Flow
* Security Considerations
* Project Structure
* Installation & Configuration
* Workflow Configuration
* Testing & Validation
* Limitations
* Future Improvements
* Author
* License

⸻

Overview

AURA addresses a common limitation of traditional automation systems: rigid, predefined execution paths that require users to specify every step.

Instead, AURA uses an AI-driven orchestration layer to interpret user intent and select relevant tools dynamically.

Core Capabilities

* Natural language interaction through Telegram.
* Dynamic AI-driven tool selection.
* Specialized lead research and qualification.
* Persistent conversational memory using MongoDB Atlas.
* User profile and preference retrieval.
* AI-generated personalized communication drafts.
* Interactive approval, rejection, and revision workflows.
* Controlled Gmail execution.
* Cross-session conversational continuity.

⸻

Key Features

Feature	Description
Agentic Orchestration	Gemini determines which available tools are relevant to a user request.
Persistent Memory	MongoDB stores conversational history across sessions.
User Context	User preferences and profile information support personalized interactions.
Dynamic Tool Selection	The agent invokes specialized tools according to task requirements.
Lead Intelligence	Apify-powered LinkedIn research and lead qualification.
Message Generation	Produces structured, personalized outreach drafts.
Human-in-the-Loop	Requires explicit user decisions before critical external actions.
Telegram Interface	Provides conversational interaction and approval controls.
Modular Architecture	Separates reasoning, tools, execution, and memory responsibilities.

⸻

System Architecture

AURA follows a four-phase architecture:

flowchart TB
    U["User"] --> TG["Telegram Bot"]
    TG --> ORC["n8n Main Orchestrator"]
    ORC <--> LLM["Google Gemini Flash"]
    subgraph P1["PHASE 1 — CONVERSATIONAL BRAIN"]
        TG
        ORC
        LLM
    end
    subgraph P2["PHASE 2 — AGENTIC REASONING"]
        ROUTER["Dynamic Tool Selection"]
        SEARCH["LinkedIn Search — Apify"]
        QUAL["Lead Qualification Engine"]
        PROFILE["User Profile Memory"]
        DRAFT["Outbound Message Drafting"]
        ROUTER --> SEARCH
        ROUTER --> QUAL
        ROUTER --> PROFILE
        ROUTER --> DRAFT
    end
    ORC --> ROUTER
    subgraph P3["PHASE 3 — HUMAN-IN-THE-LOOP"]
        REVIEW["Telegram Approval Interface"]
        DECISION{"User Decision"}
        EXEC["Gmail Executor"]
        REVISE["Revise Draft"]
        REJECT["Reject / Cancel"]
        REVIEW --> DECISION
        DECISION -->|Approve| EXEC
        DECISION -->|Revise| REVISE
        DECISION -->|Reject| REJECT
        REVISE --> REVIEW
    end
    DRAFT --> REVIEW
    subgraph P4["PHASE 4 — PERSISTENT MEMORY"]
        DB[("MongoDB Atlas")]
        HISTORY["Session History"]
        CONTEXT["User Preferences & Context"]
        STATE["Execution State"]
        DB --> HISTORY
        DB --> CONTEXT
        DB --> STATE
    end
    ORC <--> DB
    EXEC --> RESULT["Execution Result"]
    RESULT --> TG

⸻

Four-Phase Architecture

Phase 1 — Conversational Brain

The primary interaction and orchestration layer.

Responsibilities:

* Receive incoming Telegram messages.
* Interpret natural language instructions.
* Maintain conversational context.
* Identify user goals.
* Coordinate interactions with the AI model and available tools.

Core components:

* Telegram Trigger
* n8n AI Agent
* Google Gemini Chat Model
* Conversational Memory

Phase 2 — Agentic Reasoning & Tool Orchestration

The reasoning layer enables AURA to select and invoke specialized tools according to the user’s intent.

Specialized Tools

1. LinkedIn Search Tool — Apify

Retrieves relevant prospect information through configured Apify research workflows.

2. Lead Qualification Engine

Processes prospect information and structures qualification results according to the configured criteria.

3. User Profile Memory Tool

Retrieves user-specific preferences, background, and contextual information to support personalized responses.

4. Outbound Message Drafting Tool

Generates personalized communication drafts using the available prospect information and user context.

The agent determines which tools to invoke based on the task rather than following a single fixed sequence.

Phase 3 — Human-in-the-Loop Execution

AURA incorporates a human approval layer for critical external actions.

Instead of automatically sending a generated message, the system presents a draft to the user through Telegram.

Approval Lifecycle

flowchart TD
    A["User Requests an Action"] --> B["Agent Prepares Draft"]
    B --> C["Telegram Review Message"]
    C --> D{"User Decision"}
    D -->|Approve| E["Validate Approval"]
    E --> F["Gmail Execution"]
    F --> G["Return Execution Result"]
    D -->|Reject| H["Cancel Action"]
    D -->|Revise| I["Update Draft"]
    I --> C

Supported decisions:

* Approve — authorize the proposed action.
* Reject — cancel the proposed action.
* Revise — request modifications before approval.

This design separates AI-generated decisions and drafts from authorization to perform external actions.

Phase 4 — Persistent Memory & State

MongoDB Atlas provides persistent storage for conversational memory and contextual continuity.

AURA uses the Telegram Chat ID as the session identifier.

Example n8n expression:

{{ $json.message.chat.id }}

Memory Components

Component	Purpose
Session History	Retains previous conversational messages.
User Preferences	Supports personalized interactions.
Execution Context	Maintains relevant task information.
Session Identification	Associates conversation history with a Telegram chat.

MongoDB configuration:

* Database: memory
* Collection: n8n_chat_histories

⸻

Human-in-the-Loop: Why It Matters

AURA implements a controlled execution approach.

The AI agent may reason, research, prepare content, and propose an action, but critical external execution requires an explicit user decision.

This creates a separation between:

1. Intelligence — interpreting and preparing.
2. Authorization — user approval.
3. Execution — performing the approved operation.
4. Feedback — reporting the execution result.

The approval callback must be validated against the intended user, action, and pending request before execution.

⸻

Technology Stack

Layer	Technology
Orchestration Engine	n8n Cloud
AI Reasoning	Google Gemini Flash
Persistent Database	MongoDB Atlas
Conversational Interface	Telegram Bot API
Lead Research	Apify
Email Execution	Gmail API
Custom Logic	JavaScript / Python
Integration	REST APIs & Webhooks
Memory	MongoDB Chat Memory

⸻

Data Flow

sequenceDiagram
    participant User
    participant Telegram
    participant n8n
    participant Gemini
    participant Tools
    participant MongoDB
    participant Gmail
    User->>Telegram: Natural language request
    Telegram->>n8n: Incoming message
    n8n->>MongoDB: Retrieve session context
    MongoDB-->>n8n: Previous conversation
    n8n->>Gemini: User request + context
    Gemini->>Tools: Select and invoke tools
    Tools-->>Gemini: Tool results
    Gemini-->>n8n: Prepared response or action draft
    n8n->>MongoDB: Update conversation memory
    n8n->>Telegram: Present response / approval request
    Telegram->>User: Approve, Reject, or Revise
    alt Approved
        User->>Telegram: Approve
        Telegram->>n8n: Approval callback
        n8n->>n8n: Validate pending action
        n8n->>Gmail: Execute approved message
        Gmail-->>n8n: Execution result
        n8n->>Telegram: Report result
    else Rejected
        User->>Telegram: Reject
        n8n->>Telegram: Confirm cancellation
    else Revised
        User->>Telegram: Request revision
        n8n->>Gemini: Update draft
        n8n->>Telegram: Present revised draft
    end

⸻

Security Considerations

AURA uses several security-oriented design practices:

* API credentials managed through n8n credentials.
* MongoDB Atlas network access restrictions.
* SSL/TLS-encrypted service connections.
* Human approval before critical external actions.
* Webhook callback validation.
* Separation between draft generation and external execution.

Important Deployment Notes

* Never commit API keys, bot tokens, database credentials, or OAuth secrets.
* Avoid unrestricted MongoDB IP access in production.
* Validate the Telegram user and pending action before honoring approval callbacks.
* Use HTTPS for externally accessible webhook endpoints.
* Store sensitive configuration outside the workflow source code.
* Rotate credentials if they are accidentally exposed.


⸻

Installation & Configuration

Prerequisites

Before deploying AURA, prepare:

* An n8n Cloud or self-hosted instance.
* A Google Gemini API key.
* A MongoDB Atlas cluster.
* A Telegram Bot Token.
* An Apify API token.
* Gmail OAuth credentials.

Step 1 — Clone the Repository

git clone https://github.com/YOUR_USERNAME/AURA.git
cd AURA

Step 2 — Configure MongoDB Atlas

1. Create a MongoDB Atlas cluster.
2. Configure the Network Access allowlist.
3. Create a database user with appropriate permissions.
4. Retrieve the MongoDB connection string.
5. Configure the connection through n8n credentials.

Step 3 — Configure Telegram

Create a Telegram bot using BotFather.

Add the bot credentials to n8n and configure the Telegram Trigger to receive incoming messages.

Step 4 — Configure Gemini

Create an API key through Google AI Studio.

Configure the Gemini Chat Model node with the appropriate credentials and model.

Step 5 — Configure Persistent Memory

Configure the MongoDB Chat Memory node.

Session ID:

{{ $json.message.chat.id }}

Database:

memory

Collection:

n8n_chat_histories

Step 6 — Configure Specialized Tools

Set up the required credentials and connections for:

* Apify LinkedIn Search.
* Lead Qualification.
* User Profile Memory.
* Outbound Message Drafting.
* Gmail execution.

Step 7 — Configure Human Approval

Configure Telegram approval callbacks and the corresponding execution logic.

Ensure that:

* Only the intended user can approve the pending action.
* The approval refers to the correct draft.
* Rejected actions cannot reach the Gmail executor.
* Revision requests return to the review stage.
* Duplicate callbacks cannot trigger duplicate external actions.

Step 8 — Activate and Test

Import the workflow exports into n8n, configure credentials, verify node connections, and test each execution path before activating the production workflow.

⸻

Workflow Configuration

The main workflow uses the incoming Telegram message as its user input.

Example expression:

{{ $json.message.text }}

For Telegram responses, use the original chat identifier:

{{ $('Telegram Trigger').item.json.message.chat.id }}

This preserves the destination chat associated with the incoming message.

Note: Expressions may require adjustment when nodes, branches, or workflow input structures change.

⸻

Testing & Validation

AURA’s four architectural phases have been implemented and tested.

The following scenarios define the core validation scope:

Test Scenario	Expected Behavior
Telegram Input	Receives and processes a user message.
Conversational Context	Maintains relevant previous conversation.
Dynamic Tool Selection	Invokes the appropriate available tool.
Lead Research	Retrieves configured research results.
Lead Qualification	Processes prospect information.
User Profile Retrieval	Retrieves relevant user context.
Draft Generation	Produces a communication draft.
Approval	Executes only the approved pending action.
Rejection	Prevents the rejected action from executing.
Revision	Updates the draft and returns it for review.
Persistent Memory	Retains conversation history across sessions.

⸻

Limitations

AURA depends on external services and their availability, authentication, and API limitations.

* AI-generated outputs may require human verification.
* LinkedIn research depends on the configured Apify actor and its data availability.
* Gmail execution requires valid OAuth authorization.
* Persistent memory depends on MongoDB connectivity.
* n8n Cloud availability and execution limits may affect workflow operation.
* Human approval controls do not eliminate all operational or security risks.

⸻

Future Improvements

Potential extensions include:

* Dedicated multi-agent sub-workflows.
* More granular user profile management.
* Improved execution-state recovery.
* Enhanced approval audit logs.
* Retry and failure recovery mechanisms.
* Expanded tool integrations.
* Role-based authorization.
* Automated regression testing.

⸻

Author

Dalia Ammar

Data & AI Specialist | AI Automation & Machine Learning Engineer

Interested in Agentic AI, intelligent automation, AI orchestration, and applied machine learning.

* GitHub: github.com/dalia404
* LinkedIn: linkedin.com/in/dalia-ammar4004

⸻

License

Distributed under the MIT License. See LICENSE for details.

⸻

<p align="center">
  <strong>AURA</strong><br>
  From Conversational Intelligence to Controlled Autonomous Action.
</p>
