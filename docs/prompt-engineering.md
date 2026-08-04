# Prompt Engineering

# Overview

The conversational behavior of Ava is driven primarily through structured prompt engineering rather than traditional programming logic.

Instead of relying on a single, monolithic system prompt, the assistant was designed around four independent prompt sections:

* Persona
* Instructions
* Constraints
* Response Format

Separating responsibilities across these sections makes the assistant easier to maintain, easier to extend, and significantly reduces conflicts between behavioral instructions.

This document explains the reasoning behind each section and the design decisions made during implementation.

---

# Design Philosophy

The objective was not simply to create a chatbot capable of answering questions.

The assistant was designed to function as an intelligent workflow orchestrator capable of:

* Understanding user intent
* Retrieving verified information
* Collecting appointment requests
* Executing external business tools
* Enforcing healthcare safety rules
* Maintaining conversational context

Prompt engineering therefore became the primary mechanism controlling application behavior.

---

# Prompt Architecture

The final prompt is divided into four independent layers.

```
Persona

↓

Instructions

↓

Constraints

↓

Response Format
```

Each layer serves a unique purpose.

---

# Persona

The Persona establishes the assistant's identity before any conversation begins.

The assistant introduces itself as Ava, the digital front door for Amen Clinics.

Rather than pretending to be a healthcare professional, Ava clearly communicates that it is an AI assistant responsible for helping users navigate the clinic and begin the appointment process.

This distinction is particularly important within healthcare environments because users may otherwise assume they are interacting directly with qualified medical staff.

The Persona also defines communication style.

Key objectives include:

* Friendly
* Calm
* Professional
* Human-like
* Non-judgmental
* Easy to understand

Responses intentionally avoid excessive technical language unless the user introduces technical terminology first.

---

# Instruction Layer

The Instructions section defines the operational logic of the assistant.

Instead of focusing on language generation alone, the instructions describe how the assistant should behave during different stages of the conversation.

Major responsibilities include:

* Identifying the visitor
* Retrieving knowledge
* Beginning appointment workflows
* Handling returning patients
* Executing external tools
* Managing booking information

The assistant first determines whether the visitor is:

* A new patient
* An existing patient
* Someone asking general questions

Each category follows a different conversational strategy.

This allows the same AI model to support multiple user journeys without requiring separate agents.

---

# Knowledge Retrieval Strategy

One of the most important prompt decisions was requiring the assistant to consult the Knowledge Base before answering factual questions.

Rather than allowing the language model to answer from memory, the assistant retrieves verified clinic information before generating a response.

This significantly reduces hallucination while improving factual consistency.

Typical retrieval topics include:

* Brain SPECT imaging
* Services
* Clinic locations
* Insurance
* Appointment process
* Frequently asked questions

If relevant information cannot be found, the assistant is instructed to acknowledge uncertainty instead of fabricating answers.

---

# Conversational Data Collection

Traditional chatbots frequently present users with long forms.

Ava instead collects information gradually through natural conversation.

Information requested includes:

* Patient name
* Phone number
* Email
* Preferred location
* General reason for visit
* Appointment timing
* Insurance information

Another important design decision was preventing duplicate questions.

Conversation memory allows the assistant to recognize information that has already been provided and continue naturally from that point.

This creates a significantly more human interaction.

---

# Lead Qualification

Appointment requests are internally categorized before being sent to Salesforce.

Three lead categories exist.

Hot

Warm

Cold

The classification considers:

* Booking intent
* Contact information availability
* User urgency
* Scheduling preference

This classification is never revealed to the patient.

Instead, it exists solely to improve CRM workflow prioritization.

---

# Tool Orchestration

Prompt engineering is also responsible for determining when external systems should be executed.

The assistant performs automation only after collecting sufficient information.

The execution order is:

```
Salesforce

↓

Gmail

↓

Google Calendar

↓

Google Sheets
```

Each tool performs a distinct operational responsibility.

This sequential workflow prevents unnecessary automation while ensuring all required business systems remain synchronized.

---

# Constraint Layer

The Constraints section functions as the assistant's operational safety policy.

Unlike conversational instructions, these rules are treated as mandatory.

Several important behaviors are enforced.

The assistant never:

* Diagnoses conditions
* Interprets symptoms
* Suggests medication changes
* Fabricates patient information
* Guesses insurance coverage
* Reveals internal lead scoring

Whenever a request falls outside its permitted scope, the assistant redirects the user to qualified clinic staff.

---

# Crisis Override

The most critical constraint is the Crisis Override.

Every incoming user message is evaluated for language indicating immediate danger.

Examples include:

* Self-harm
* Suicide
* Immediate medical emergencies
* Danger to others

If detected, the assistant immediately exits the normal workflow.

Appointment booking is stopped.

External tools are not executed.

Instead, the assistant delivers a predefined emergency response directing the user toward appropriate emergency resources.

Placing this behavior inside the Constraints section ensures that safety always has higher priority than conversational objectives.

---

# Response Formatting

The final prompt section controls communication style rather than decision making.

Responses are intentionally designed to remain:

* Concise
* Conversational
* Friendly
* Easy to read

Long paragraphs are avoided.

Information is delivered in short responses that resemble natural conversation.

Bullet points are used only when presenting lists such as clinic locations or available services.

Appointment booking is performed conversationally rather than displaying rigid questionnaires.

---

# Prompt Evolution

The implementation evolved during development.

The original design followed a multi-agent architecture with dedicated routing components responsible for directing users toward different specialized conversational paths.

During implementation it became apparent that the available Tars workspace differed from earlier platform documentation and tutorials.

The routing components described in some resources were not available within the current builder.

Rather than treating this as a blocker, the architecture was redesigned around a single AI Agent.

Structured prompting replaced explicit routing.

The assistant now determines patient type, workflow transitions, and business logic entirely through prompt engineering while preserving the same user experience.

This simplified the implementation while maintaining the intended functionality.

---

# Design Tradeoffs

Several tradeoffs were intentionally accepted.

A single orchestrator agent is simpler to maintain than multiple specialized agents.

Structured prompting reduces workflow complexity but increases prompt complexity.

Retrieval-Augmented Generation improves factual reliability while introducing dependency on the knowledge base.

Conversation memory improves user experience but requires careful handling to avoid unnecessary repetition.

Healthcare safety constraints occasionally prioritize conservative responses over conversational flexibility.

These tradeoffs were considered acceptable because reliability and patient safety were prioritized over maximizing conversational creativity.

---

# Future Improvements

Future iterations of the prompt architecture could introduce:

* Dynamic tool selection based on confidence scores.
* Retrieval confidence thresholds before answering factual questions.
* Multi-language prompting.
* Context-aware follow-up generation.
* Personalized patient experiences for authenticated users.
* Adaptive response length depending on conversation history.
* Integration with structured scheduling APIs instead of tentative calendar events.

---

# Conclusion

Prompt engineering serves as the central control mechanism behind Ava.

Rather than simply instructing the language model how to answer questions, the prompt defines the assistant's identity, operational workflow, business logic, safety constraints, response style, and integration strategy.

This structured approach transforms the language model from a conversational chatbot into a workflow-oriented AI assistant capable of supporting real administrative processes while maintaining safe and grounded interactions within a healthcare environment.
