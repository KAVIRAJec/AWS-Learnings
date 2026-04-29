## Amazon Lex & Amazon Connect

---

## Amazon Lex

Amazon Lex is a managed service for building **conversational interfaces** (chatbots and voice assistants) using the same ASR(Automatic Speech Recognition) and NLU technology that powers Alexa.

**Key Concepts:**
- **Bot**: Core resource — defines all conversation logic for an application.
- **Intent**: A specific action the user wants to perform (e.g., `BookFlight`, `CheckBalance`). A bot can have many intents.
- **Utterances**: Sample phrases that trigger an intent — Lex trains its NLU model on these (e.g., "Book me a flight", "I want to fly to New York").
- **Slot**: A parameter needed to fulfill an intent (e.g., `departure_city`, `travel_date`). Lex auto-prompts the user for missing slots.
- **Slot Type**: The allowed values for a slot — built-in types (dates, numbers) or custom types (sizes: small/medium/large).
- **Fulfillment**: What happens when all slots are filled — invoke a **Lambda function** or return control to the app.
- **Session**: Tracks filled slot values and conversation state across multiple turns.

**How it Works:**
1. User sends voice or text → **ASR** converts voice to text.
2. **NLU** identifies the intent and extracts slot values.
3. Lex prompts for any missing slots.
4. All slots filled → calls Lambda → business logic executes → response returned.

**Lex V2:**
- Visual conversation builder, streaming API for real-time voice, multi-language support within a single bot, conditional branching for slot elicitation.

**Integrations:**
- **Amazon Connect**: IVR routing in call centers — Lex handles triage before live agent handoff.
- **Lambda**: Fulfillment logic — query databases, call APIs, trigger workflows.
- Channels: **Facebook Messenger, Slack, Twilio, Kik** (native integrations).

**Use cases:** Customer service chatbots, call center IVR, HR/IT helpdesk bots, appointment booking, order management assistants.

---

## Amazon Connect

Amazon Connect is a **cloud-based contact center** service — set up and run a customer call center without any upfront infrastructure. Pay only for time customers interact with the system.

**Key Concepts:**
- **Contact Flow**: Defines how a customer interaction is handled — built in a visual drag-and-drop editor. Supports branching, prompts, queue routing, and Lambda integrations.
- **Queue**: Holds contacts waiting for an agent. Routing profiles map agents to queues based on skills.
- **Agent**: Handles contacts (calls, chats) through the browser-based **Contact Control Panel (CCP)** softphone.
- **Instance**: An isolated Connect environment — multiple instances per account per region supported.

**Channels:**
- **Voice**: Inbound and outbound calls via claimed DID or toll-free numbers.
- **Chat**: Real-time text chat and async messaging (SMS, Apple Messages).
- **Tasks**: Route non-voice work items (emails, callbacks, follow-ups) through the same agent workflow.

**AI/ML Integrations:**
- **Amazon Lex**: Embed Lex bots in contact flows for automated IVR — hands off to a human agent when needed.
- **Contact Lens**: Built-in ML — real-time transcription, sentiment analysis, keyword detection, supervisor alerts, and post-call analytics. No separate setup required.
- **Amazon Transcribe**: Real-time call transcription during live conversations.
- **Amazon Comprehend**: Post-call sentiment and entity extraction on transcripts.

**Routing:**
- **Skills-based routing**: Route contacts to agents with specific skills (language, product expertise).
- **Routing Profiles**: Define which queues an agent handles and in what priority order.

**Storage & Reporting:**
- Call recordings and chat transcripts stored automatically in **S3**.
- Contact Trace Records (CTRs) streamed to **Kinesis** for real-time analytics or stored in S3 for historical reporting.
- Built-in dashboards for real-time and historical metrics (AHT, queue depth, agent occupancy).

**Use cases:** Customer service call centers, outbound sales/collections campaigns, IT helpdesk support lines, appointment reminders via outbound calling.
