# System Workflow

# Overview

This document describes the complete execution flow of the Ava Healthcare Assistant, beginning from the moment a user opens the chat interface until the conversation concludes or a business workflow is executed.

Unlike a traditional chatbot that simply generates text, Ava operates as an orchestration layer capable of retrieving verified information, maintaining conversational context, interacting with external systems, and enforcing healthcare-specific safety rules throughout every interaction.

The workflow is intentionally modular so that each stage has a clearly defined responsibility.

---

# Conversation Lifecycle

Every conversation follows five major stages.

1. Session Initialization

2. Intent Understanding

3. Knowledge Retrieval

4. Workflow Execution

5. Conversation Completion

Each stage is explained in detail below.

---

# Stage 1: Session Initialization

When a visitor opens the assistant, a new conversation session is created.

The assistant introduces itself as Ava, the digital front door for Amen Clinics.

Its opening message is intentionally designed to determine the user's context as early as possible.

Example:

> "Hi, I'm Ava. Think of me as your first stop with our Brain Health Advisor team here at Amen Clinics. Are you new here, already a patient, or just have a quick question?"

This initial question establishes the conversation path without overwhelming the user.

The objective is to minimize unnecessary questions later in the interaction.

---

# Stage 2: Understanding User Intent

After receiving the first user message, the assistant determines what type of interaction is taking place.

The conversation generally falls into one of the following categories.

## New Patient

Typical examples include:

* Learning about Amen Clinics
* Asking about Brain SPECT imaging
* Looking for clinic locations
* Understanding pricing
* Requesting an appointment

For these conversations, the assistant assumes no previous relationship with the clinic.

---

## Existing Patient

Typical requests include:

* Rescheduling appointments
* Follow-up appointments
* Questions regarding previous visits
* Billing questions
* Medical record requests

Whenever account-specific information is requested, the assistant intentionally avoids attempting to answer using the language model.

Instead, the patient is informed that the clinic's care team will need to assist them directly.

---

## General Information

Some users simply want information.

Examples include:

* What conditions are treated?
* What is Brain SPECT?
* What insurance do you accept?

In these conversations, no booking workflow is initiated unless genuine appointment intent appears later.

---

# Stage 3: Knowledge Retrieval

Whenever the user asks a factual question, the assistant first consults the knowledge base.

Examples include:

* Clinic locations
* Services
* Brain SPECT imaging
* Insurance
* Appointment process
* Frequently asked questions

The retrieved context is then provided to the language model before generating a response.

This Retrieval-Augmented Generation workflow ensures that answers remain grounded in official clinic documentation.

If relevant information cannot be found, the assistant explicitly communicates that it cannot verify the answer instead of fabricating one.

---

# Stage 4: Conversation Memory

Throughout the interaction, conversation memory stores previously collected information.

For example, if the user has already shared:

* Name
* Phone number
* Email
* Preferred location

the assistant remembers these values instead of requesting them again.

This significantly improves the conversational experience and avoids repetitive questioning.

Conversation memory also enables users to naturally change topics before returning to appointment booking.

Example:

User:

"I'd like to book an appointment."

Later:

"What exactly is Brain SPECT?"

After answering the informational question, the assistant resumes the booking process without asking for information that has already been collected.

---

# Stage 5: Appointment Detection

Not every conversation requires automation.

The assistant begins the booking workflow only after detecting genuine appointment intent.

Typical indicators include:

* I'd like to book an appointment.
* I want to schedule an evaluation.
* Can someone contact me?
* I'd like to visit the Seattle clinic.

Once booking intent is confirmed, the assistant transitions into data collection.

---

# Stage 6: Conversational Data Collection

Rather than presenting a traditional form, Ava gathers information naturally over multiple conversational turns.

Information collected includes:

Patient name

Phone number

Email address

Preferred clinic

General reason for visit

Preferred appointment timing

Insurance information

If the user voluntarily provides multiple pieces of information in a single message, those details are extracted automatically without asking unnecessary follow-up questions.

This makes the conversation feel substantially more natural than conventional chatbot forms.

---

# Stage 7: Internal Lead Classification

Before interacting with Salesforce, every booking request is internally classified.

Three lead categories exist.

## Hot

The user demonstrates strong appointment intent together with urgency.

Examples include:

"I need to be seen this week."

or

"I've been waiting months."

---

## Warm

The user clearly wants an appointment but does not communicate urgency.

---

## Cold

Users asking informational questions without expressing appointment intent.

Importantly, this classification is entirely internal.

Patients never see or are informed about their assigned category.

---

# Stage 8: Salesforce Automation

After the required information has been collected, Ava executes Salesforce automation.

The following objects are created automatically.

## Lead

Stores information including:

Patient name

Phone

Email

Preferred location

Lead source

Lead temperature

Appointment summary

---

## Task

A follow-up task is linked to the Lead.

The task stores:

Priority

Conversation summary

Transcript

Status

This allows clinic staff to immediately continue the conversation without manually copying information from chat.

---

# Stage 9: Gmail Notification

Following successful CRM creation, the assistant sends an email notification.

The email contains:

Patient information

Contact details

Insurance

Lead temperature

Appointment summary

The objective is to ensure clinic staff receive immediate notification even before accessing Salesforce.

---

# Stage 10: Google Calendar

If scheduling information has been provided, Ava creates a tentative calendar event.

The calendar entry contains:

Patient name

Requested clinic

Requested appointment window

Insurance information

Conversation summary

When the patient specifies a preferred date or time, those values are incorporated into the calendar event.

Otherwise, a tentative placeholder event is created.

---

# Stage 11: Google Sheets Logging

Each successful booking is also appended to a Google Sheet.

The spreadsheet acts as a lightweight operational log containing:

Booking timestamp

Patient name

Contact information

Insurance

Clinic location

Lead category

Requested timing

Conversation summary

Although Salesforce remains the primary CRM, the spreadsheet provides an additional reporting layer for administrative purposes.

---

# Safety Workflow

Healthcare systems require stronger safety guarantees than standard conversational agents.

For this reason, every incoming user message is evaluated against predefined safety constraints.

If emergency language suggesting immediate danger is detected, the assistant immediately exits the normal workflow.

Instead of continuing appointment booking or answering questions, it returns a predefined crisis response directing the user toward emergency services.

No automation workflows are executed during that interaction.

This behavior has higher priority than every other instruction in the system.

---

# End of Conversation

A conversation concludes after one of the following outcomes.

The user receives the requested information.

An appointment request is successfully recorded.

A callback request is initiated.

The user ends the conversation.

Regardless of how the interaction concludes, the assistant remains available for additional questions while preserving conversational context during the active session.

---

# Workflow Summary

The complete execution sequence can be summarized as follows.

```text
User

↓

AI Agent

↓

Intent Detection

↓

Knowledge Retrieval

↓

Grounded Response

↓

Booking Intent?

↓

No
│
└── Continue Conversation

Yes
│
▼

Collect Patient Information

↓

Conversation Memory

↓

Lead Classification

↓

Salesforce Lead

↓

Salesforce Task

↓

Gmail Notification

↓

Google Calendar Event

↓

Google Sheets Log

↓

Booking Confirmation
```

# Design Principles

The workflow was designed around five guiding principles.

* Minimize hallucinations through Retrieval-Augmented Generation.

* Maintain a natural conversational experience instead of rigid forms.

* Automate repetitive administrative work.

* Protect patient safety through strict operational constraints.

* Integrate conversational AI directly into existing business systems rather than replacing them.

These principles ensure that Ava functions not simply as a chatbot, but as an intelligent workflow assistant capable of supporting both patients and clinic staff through a unified conversational interface.
