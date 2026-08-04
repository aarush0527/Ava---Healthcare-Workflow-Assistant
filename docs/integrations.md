# External Integrations

# Overview

Ava was designed as more than a conversational assistant. One of the primary objectives of this project was to demonstrate how an AI agent can integrate with existing business systems to automate operational workflows.

Rather than requiring clinic staff to manually transfer information from conversations into multiple software platforms, the assistant performs these actions automatically after collecting the required information from the patient.

Each integration has a clearly defined responsibility within the overall system architecture.

---

# Integration Architecture

```text
                     User
                       │
                       ▼
                 Ava AI Assistant
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
 Knowledge Base   Conversation Memory
        │
        ▼
 Booking Workflow
        │
        ▼
──────────────────────────────────────────

Salesforce CRM

↓

Gmail Notification

↓

Google Calendar Event

↓

Google Sheets Logging
```

The AI Agent acts as the orchestration layer that determines when each integration should be executed.

No external tool is invoked unless the conversation satisfies the required conditions.

---

# Knowledge Base Integration

## Purpose

The Knowledge Base enables Retrieval-Augmented Generation.

Instead of relying entirely on the language model's internal knowledge, the assistant retrieves relevant documentation from the Amen Clinics knowledge base before generating factual responses.

This significantly improves reliability and reduces hallucination.

Typical queries include:

* Brain SPECT imaging
* Clinic locations
* Services
* Insurance information
* Pricing
* Frequently asked questions
* Appointment process

If requested information cannot be retrieved, the assistant informs the user rather than generating unsupported content.

---

# Salesforce CRM

## Purpose

Salesforce serves as the primary Customer Relationship Management platform.

Every appointment request is converted into structured CRM records, allowing clinic staff to continue the workflow without manually copying information from the conversation.

---

## Lead Creation

Whenever sufficient booking information has been collected, the assistant creates a Lead.

Typical Lead fields include:

First Name

Last Name

Email

Phone Number

Company

Lead Source

Lead Rating

Description

The description contains a concise summary of the conversation together with relevant appointment details.

---

## Lead Qualification

Before creating the Lead, the assistant internally categorizes the booking request.

Possible categories include:

Hot

Warm

Cold

The classification depends on factors such as:

* Booking intent
* Contact information availability
* User urgency
* Scheduling preference

This information is used internally by clinic staff and is never disclosed to patients.

---

## Task Creation

Immediately after Lead creation, a Salesforce Task is created and linked to the Lead.

The Task contains:

Subject

Priority

Status

Conversation summary

Conversation transcript

This allows follow-up staff to understand the entire conversation without reopening the chatbot.

---

# Gmail Integration

## Purpose

Email notifications provide immediate visibility into new appointment requests.

Instead of requiring staff to continuously monitor Salesforce, important booking requests are delivered directly through email.

The notification contains:

Patient name

Phone number

Email address

Preferred location

Insurance information

Lead temperature

Conversation summary

This enables faster response times and reduces the likelihood of missed appointment requests.

---

# Google Calendar

## Purpose

Google Calendar is used as a lightweight scheduling layer.

Once appointment information becomes available, a tentative calendar event is created automatically.

Whenever possible, the assistant incorporates:

Preferred appointment date

Preferred appointment time

Clinic location

Patient information

If the patient does not specify an exact date, the assistant creates a tentative placeholder event for the requested time window.

This provides staff with an early scheduling reference before final confirmation.

---

# Google Sheets

## Purpose

Google Sheets provides an additional reporting layer.

Each successful appointment request is appended as a new row.

Typical columns include:

Booking timestamp

Patient name

Email

Phone number

Preferred location

Insurance

Lead category

Requested appointment timing

Conversation summary

Although Salesforce remains the primary operational database, the spreadsheet offers a simple overview of booking activity and can be used for reporting, analytics, or auditing.

---

# Conversation Memory

Conversation memory is an important part of the assistant's overall user experience.

Previously collected information remains available throughout the conversation.

Examples include:

Patient name

Email

Phone number

Insurance provider

Preferred clinic

Requested appointment timing

This eliminates repetitive questioning and enables the assistant to resume interrupted workflows naturally.

---

# Prompt Engineering

The behavior of the assistant is governed through structured prompt engineering.

The prompt is divided into four logical sections.

Persona

Defines the assistant's identity and communication style.

Instructions

Describe operational behavior, booking flow, workflow execution, and business logic.

Constraints

Implement healthcare safety policies and define strict operational boundaries.

Response Format

Ensures replies remain conversational, concise, and easy to understand.

This separation makes the prompt easier to maintain while reducing conflicts between behavioral instructions.

---

# Healthcare Safety

Healthcare assistants require stronger operational safeguards than general-purpose chatbots.

Several protections were incorporated into the system.

The assistant never:

Provide diagnoses

Interpret symptoms

Recommend medication changes

Fabricate patient records

Invent insurance information

Promise treatment outcomes

Whenever users request information requiring access to protected records, the assistant redirects them to the appropriate clinic staff.

---

# Crisis Handling

Emergency situations always take priority over operational workflows.

If crisis language indicating immediate danger is detected, the assistant immediately interrupts the conversation.

Instead of continuing appointment booking, it returns a predefined emergency response directing users toward emergency services.

No CRM records are created during these interactions.

This behavior is enforced through prompt constraints rather than conversational reasoning.

---

# Design Decisions

Several design choices were made during implementation.

Retrieval-Augmented Generation was selected instead of relying entirely on the language model because healthcare information requires grounded responses.

Salesforce was chosen as the primary operational database because it naturally supports patient follow-up workflows.

Conversation memory was enabled to improve user experience and reduce repetitive questioning.

Booking information is collected conversationally rather than using traditional forms to create a more natural interaction.

Business integrations are executed only after sufficient information has been collected, minimizing unnecessary automation.

---

# Current Limitations

Although the assistant successfully automates several business workflows, some limitations remain.

The assistant does not directly access electronic medical records.

Calendar events are created tentatively rather than confirming appointments automatically.

Insurance eligibility is not verified in real time.

Knowledge retrieval depends entirely on the available knowledge base.

Appointment availability is not synchronized with a live scheduling system.

These limitations were intentionally accepted to prioritize safety and keep the implementation focused on administrative automation.

---

# Future Enhancements

Several improvements could extend the current architecture.

Integration with Electronic Health Record systems.

Live appointment availability and scheduling.

SMS notifications alongside email.

Patient authentication for returning users.

Analytics dashboard for conversation insights.

Multi-language support.

Voice interaction.

Secure document upload.

Automatic follow-up reminders.

Role-based access for clinic staff.

---

# Conclusion

Ava demonstrates how conversational AI can function as an intelligent workflow automation platform rather than simply a chatbot.

By combining Retrieval-Augmented Generation, conversation memory, prompt engineering, CRM automation, productivity tools, and healthcare-specific safety mechanisms, the system provides a complete patient engagement workflow while remaining grounded, transparent, and operationally useful.

The architecture is intentionally modular, allowing additional integrations and business processes to be incorporated with minimal changes to the conversational experience.
