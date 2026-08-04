# System Architecture

# Overview

Ava is an AI-powered healthcare assistant designed for Amen Clinics. The system combines Retrieval-Augmented Generation (RAG), prompt engineering, conversation memory, CRM automation, and productivity tool integrations to provide patients with a conversational interface for obtaining information and requesting appointments.

Unlike a traditional chatbot that relies entirely on a language model, Ava grounds its responses using a dedicated knowledge base and performs real actions through external business tools. The assistant is capable of answering questions about Amen Clinics, collecting appointment requests, creating Salesforce records, notifying staff through Gmail, scheduling tentative appointments in Google Calendar, and logging booking activity for operational tracking.

The overall objective of the project was to demonstrate how modern AI agents can move beyond conversation and automate real business workflows while maintaining safe and responsible behavior in a healthcare environment.

---

# High Level Architecture

```text
                     User
                       │
                       ▼
               Ava AI Assistant
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
 Knowledge Base   Conversation   Prompt Logic
   Retrieval         Memory
        │
        ▼
 Grounded Response
        │
        ▼
 Booking Intent Detected
        │
        ▼
────────────────────────────────────────────
 Business Integrations
────────────────────────────────────────────

Salesforce
      │
      ├── Create Lead
      └── Create Task

Gmail
      │
      └── Appointment Notification

Google Calendar
      │
      └── Tentative Appointment Event

Google Sheets
      │
      └── Booking Log
```

---

# Core Components

## AI Agent

The AI Agent acts as the central orchestration layer of the application.

Its responsibilities include:

* Managing the conversation.
* Identifying user intent.
* Retrieving relevant knowledge.
* Maintaining conversational context.
* Executing external tools.
* Applying healthcare safety constraints.
* Coordinating appointment booking.

Rather than acting as a simple question answering system, the agent behaves as a workflow coordinator capable of interacting with multiple business systems during a single conversation.

---

## Retrieval-Augmented Knowledge Base

Healthcare assistants should not generate unsupported medical or organizational information.

To minimize hallucinations, the assistant retrieves information from a dedicated knowledge base before answering factual questions.

The knowledge base contains information such as:

* Clinic locations
* Brain SPECT imaging
* Services
* Insurance information
* Frequently asked questions
* Appointment process
* Clinic policies

If requested information is unavailable, the assistant explicitly communicates that it cannot verify the answer instead of attempting to generate one.

This approach provides more reliable and transparent responses while remaining grounded in documented clinic information.

---

## Conversation Memory

Conversation memory enables the assistant to maintain context throughout a user's interaction.

Information that has already been provided by the user is remembered for the remainder of the conversation.

Examples include:

* Patient name
* Email address
* Phone number
* Preferred clinic
* Insurance provider
* Appointment timing

Maintaining conversational state reduces repetitive questioning and creates a more natural booking experience.

---

# Prompt Engineering Strategy

The behavior of the assistant is primarily controlled through structured prompt engineering.

Instead of a single instruction block, the prompt is divided into four independent sections.

## Persona

Defines the identity of the assistant.

The assistant represents Amen Clinics as an AI-powered Brain Health Advisor while remaining transparent that it is not a medical professional.

---

## Instructions

Defines operational behavior including:

* Identifying patient type
* Handling new patients
* Handling returning patients
* Appointment booking
* Lead qualification
* Tool execution
* Knowledge retrieval

---

## Constraints

The constraint layer acts as the system's safety policy.

It prevents the assistant from:

* Providing diagnoses
* Interpreting symptoms
* Discussing medication changes
* Fabricating patient records
* Revealing internal lead scoring

The constraint section also contains the crisis override that immediately interrupts the conversation whenever emergency language is detected.

---

## Response Format

The assistant is instructed to communicate using concise, conversational language.

Long paragraphs are avoided.

Information is presented naturally rather than as rigid forms or questionnaires.

---

# Patient Classification

One of the first objectives of the assistant is determining the type of visitor.

The conversation is classified into one of three categories:

## New Patient

Users unfamiliar with Amen Clinics receive explanations about services, locations, Brain SPECT imaging, pricing, insurance, and appointment options.

---

## Existing Patient

Returning patients are acknowledged without repeating introductory information.

Questions involving protected account information such as billing, medical records, or prescriptions are redirected to the clinic's care team.

---

## General Inquiry

Users seeking only informational answers are served directly from the knowledge base without encouraging unnecessary appointment booking.

---

# Appointment Workflow

Once genuine booking intent is detected, the assistant transitions from information retrieval to workflow automation.

Information is collected conversationally in the following order:

1. Name
2. Email or phone number
3. Preferred location
4. Reason for visit
5. Timing preference
6. Insurance information

Instead of displaying a form, information is collected naturally across multiple conversational turns.

---

# Lead Qualification

Every appointment request is internally categorized according to urgency.

The categories are:

* Hot
* Warm
* Cold

This classification influences Salesforce task priority while remaining completely invisible to the patient.

---

# External Integrations

After a successful booking request, the assistant performs multiple automated actions.

## Salesforce

Creates a Lead containing patient information.

Creates a related Task for follow-up.

---

## Gmail

Sends an appointment notification email containing a structured booking summary.

---

## Google Calendar

Creates a tentative calendar event using the requested appointment details whenever scheduling information is available.

---

## Google Sheets

Appends a booking record for lightweight operational reporting.

---

# Safety Architecture

Healthcare assistants require stronger safeguards than general-purpose chatbots.

Several protective mechanisms are implemented.

## Crisis Detection

Messages indicating immediate danger immediately interrupt the normal workflow.

The assistant displays a predefined emergency response instead of continuing with booking.

---

## Medical Boundaries

The assistant never:

* Diagnoses conditions
* Interprets symptoms
* Recommends medication changes
* Makes clinical decisions

Patients requiring medical guidance are redirected to qualified healthcare professionals.

---

## Grounded Responses

All factual clinic information originates from the knowledge base rather than unsupported model generation.

This significantly reduces hallucination risk.

---

# Design Philosophy

The primary goal of Ava is not to replace healthcare professionals.

Instead, it serves as an intelligent digital front door that automates repetitive administrative work while allowing medical staff to focus on patient care.

The architecture demonstrates how modern AI agents can combine conversational intelligence with business workflow automation through CRM systems, productivity tools, retrieval-based knowledge systems, and carefully designed safety constraints.

This design allows a single conversational interface to move seamlessly from answering informational questions to executing real operational workflows without requiring users to switch between multiple systems.
