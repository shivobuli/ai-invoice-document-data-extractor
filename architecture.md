AI Invoice / Document Data Extractor — Architecture

1\. Purpose



This document describes the architecture of the AI Invoice / Document Data Extractor portfolio project.



The workflow demonstrates how invoice text can be processed by an AI Agent, converted into structured JSON, validated, and routed to a human-review path when important information is missing.



The implementation is intentionally lightweight and uses synthetic invoice data.



2\. High-Level Architecture

Client / Document Source

&#x20;         ↓

&#x20;      Webhook

&#x20;         ↓

AI Invoice Data Extraction Agent

&#x20;         ↓

&#x20;Structured Output Parser

&#x20;         ↓

Validate Extracted Invoice

&#x20;         ↓

&#x20;    ┌────┴─────┐

&#x20;    ↓          ↓

&#x20;  Valid     Incomplete

&#x20;    ↓          ↓

JSON Response  Human Review

&#x20;              Required

3\. Workflow Components

3.1 Webhook



The Webhook node is the entry point for the workflow.



It accepts a POST request containing the invoice or document text.



Expected input structure:



{

&#x20; "document": "invoice document text"

}



The webhook provides a simple integration point for future document sources such as email processors, upload applications, CRM systems, or other automation workflows.



3.2 AI Invoice Data Extraction Agent



The AI Agent receives the document text and extracts the required invoice information.



The agent is instructed to:



Use only information present in the document.

Avoid inventing missing information.

Preserve invoice details accurately.

Extract all available line items.

Return missing fields as null.

Return only the required structured information.



The current implementation uses GPT-5 mini through n8n Gateway credits.



3.3 Structured Output Parser



The Structured Output Parser constrains the AI Agent response to the expected JSON structure.



The schema contains:



document\_type

invoice\_number

invoice\_date

due\_date

vendor

customer

currency

line\_items

subtotal

tax

total\_amount

payment\_status

payment\_terms



The schema permits null values for information that is not present in the source document.



This is important because real-world documents may be incomplete.



4\. Data Extraction Structure



The workflow produces a structured record similar to:



{

&#x20; "document\_type": "invoice",

&#x20; "invoice\_number": "INV-2026-0017",

&#x20; "invoice\_date": "06 October 2026",

&#x20; "due\_date": "20 October 2026",

&#x20; "vendor": {

&#x20;   "name": "OBX Digital Solutions",

&#x20;   "address": "..."

&#x20; },

&#x20; "customer": {

&#x20;   "name": "GreenLeaf Business Services",

&#x20;   "address": "..."

&#x20; },

&#x20; "currency": "INR",

&#x20; "line\_items": \[

&#x20;   {

&#x20;     "description": "AI Workflow Automation Consultation",

&#x20;     "quantity": 1,

&#x20;     "unit\_price": 25000

&#x20;   }

&#x20; ],

&#x20; "subtotal": 35000,

&#x20; "tax": 6300,

&#x20; "total\_amount": 41300,

&#x20; "payment\_status": "Unpaid",

&#x20; "payment\_terms": "Payment due within 14 days."

}

5\. Validation Layer



After extraction, the workflow evaluates whether a usable invoice total has been extracted.



The validation condition is:



$json.output.total\_amount > 0

Valid Path



When the condition is true, the extracted invoice data is returned directly as JSON.



Review Path



When the condition is false, the workflow routes the document to the Human Review Required node.



The review path preserves the extracted information and adds:



{

&#x20; "review\_status": "Human review required"

}



This prevents incomplete extraction from being treated as a fully validated invoice.



6\. Human-in-the-Loop Design



The human-review mechanism is currently implemented as a workflow status rather than an external notification.



The workflow identifies documents requiring attention and marks them with:



Human review required



A production implementation could connect this stage to:



Gmail

Slack

Microsoft Teams

Helpdesk software

CRM

Approval systems

Accounting workflows



The current project does not claim that a real employee is automatically notified.



7\. Data Flow



The complete data flow is:



Document Text

&#x20;    ↓

Webhook Input

&#x20;    ↓

AI Agent

&#x20;    ↓

Structured Invoice JSON

&#x20;    ↓

Validation

&#x20;    ↓

&#x20;┌───────────────┐

&#x20;│               │

Valid         Incomplete

&#x20;│               │

&#x20;↓               ↓

JSON         Review Status

Response     + Extracted Data

8\. Error and Missing-Data Handling



The extraction prompt explicitly instructs the AI Agent not to invent missing information.



For example, if an invoice does not contain a due date:



{

&#x20; "due\_date": null

}



Similarly, if the total amount cannot be extracted:



{

&#x20; "total\_amount": null

}



The validation layer can then detect the missing total and route the document for review.



This provides a basic safeguard against blindly accepting incomplete AI-generated records.



9\. Current Input Limitation



The current workflow accepts document text.



It does not directly process:



PDF files

Invoice images

Scanned documents

Handwritten invoices



OCR or document parsing can be added later before the AI extraction stage.



10\. Security and Privacy Considerations



The portfolio version uses synthetic invoice information.



No real customer invoices, bank information, payment credentials, or confidential business documents are included.



For a production implementation, additional controls would be required, including:



Access control

Secure document storage

Data retention policies

Sensitive-data handling

Audit logging

Encryption

Appropriate API and vendor privacy controls

11\. Future Architecture



A more advanced production version could use:



Email / Upload

&#x20;     ↓

PDF / Image Parser

&#x20;     ↓

OCR

&#x20;     ↓

AI Document Extraction

&#x20;     ↓

Structured Output

&#x20;     ↓

Validation

&#x20;     ↓

Confidence Check

&#x20;     ↓

&#x20;┌────┴─────────┐

&#x20;↓              ↓

Auto Process   Human Review

&#x20;↓              ↓

Database       Approval

&#x20;↓

Accounting / ERP / CRM

12\. Portfolio Architecture Summary



The project demonstrates a practical AI automation pipeline consisting of:



Input → AI Extraction → Structured Output → Validation → Human Review



The architecture deliberately separates AI extraction from deterministic validation. This makes the workflow easier to understand, test, and extend.



Project Status: Complete / Frozen

