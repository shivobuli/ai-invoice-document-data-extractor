AI Invoice / Document Data Extractor



An AI-powered document data extraction workflow built with n8n. The workflow accepts invoice/document text through a webhook, uses an AI Agent to extract structured information, validates the extracted data, and routes incomplete documents for human review.



Project Overview



Businesses often receive invoices and documents containing important information in semi-structured text. Manually entering this information into systems can be repetitive and time-consuming.



This project demonstrates how AI automation can convert invoice text into a consistent structured JSON record while applying a validation step and human-review fallback.



The project is designed as a lightweight portfolio demonstration using synthetic invoice data.



Workflow Architecture

Invoice / Document

&#x20;       ↓

Webhook

&#x20;       ↓

AI Invoice Data Extraction Agent

&#x20;       ↓

Structured JSON

&#x20;       ↓

Validate Extracted Invoice

&#x20;       ↓

&#x20;  ┌────┴────┐

&#x20;  ↓         ↓

Valid     Incomplete

&#x20;  ↓         ↓

Response   Human Review

&#x20;           Required

Key Features

Webhook-based document input

AI-powered invoice information extraction

Structured JSON output

Extraction of multiple invoice line items

Separate vendor and customer information

Currency identification

Subtotal, tax, and total extraction

Payment status and payment terms extraction

Missing-field handling using null

Validation of extracted invoice data

Human-review routing for incomplete documents

Synthetic test data for safe portfolio demonstration

No external paid API key required for the demonstrated workflow

Extracted Fields



The workflow extracts:



Document type

Invoice number

Invoice date

Due date

Vendor name and address

Customer name and address

Currency

Line-item descriptions

Quantity

Unit price

Subtotal

Tax

Total amount

Payment status

Payment terms

Validation Logic



After the AI Agent produces the structured invoice data, the workflow checks whether a valid positive total amount has been extracted.



If:



total\_amount > 0



the invoice follows the normal response path.



If the total amount is missing or invalid, the workflow routes the document to the human-review path and adds:



{

&#x20; "review\_status": "Human review required"

}



This demonstrates a basic human-in-the-loop pattern where automation does not blindly process incomplete information.



Technology Stack

n8n

AI Agent

OpenAI GPT-5 mini through n8n Gateway credits

Structured Output Parser

Webhook

Conditional validation

JSON

PowerShell for workflow testing

Current Scope



The current implementation accepts document text through a webhook.



It does not currently perform:



PDF parsing

Image processing

OCR

Handwritten invoice recognition

Email attachment ingestion

Accounting-system integration

Automatic database storage

Automatic human notification



These can be added as future extensions.



Testing



The workflow was tested with two synthetic invoice scenarios.



Test 1 — Complete Invoice



A complete synthetic invoice containing invoice details, vendor information, customer information, two line items, subtotal, GST, total amount, payment status, and payment terms was submitted.



Result: Successful structured extraction.



The workflow correctly extracted the invoice number, dates, vendor, customer, currency, line items, subtotal, tax, total amount, payment status, and payment terms.



Test 2 — Incomplete Invoice



A deliberately incomplete synthetic invoice was submitted without several fields including due date, subtotal, tax, total amount, payment status, and payment terms.



Result: Human-review path triggered successfully.



The extracted information was preserved and the workflow returned:



review\_status: Human review required



This confirms that the validation and human-review fallback are functioning.



Example Use Case



A small business could extend this workflow to process invoices received through email or a document-upload system.



A future production architecture could look like:



Email / Upload

&#x20;     ↓

PDF / Image / Document Extraction

&#x20;     ↓

OCR if Required

&#x20;     ↓

AI Invoice Extraction

&#x20;     ↓

Validation

&#x20;     ↓

&#x20;  ┌──┴──┐

&#x20;  ↓     ↓

Valid  Review

&#x20;  ↓     ↓

Database  Human

&#x20;  ↓

Accounting / ERP

Portfolio Value



This project demonstrates practical AI automation beyond simple text generation.



It shows:



AI Agent usage

Structured output generation

Document intelligence

Data extraction

Validation logic

Conditional workflow routing

Human-in-the-loop automation

Webhook-based integration

Handling of incomplete information



The project is intentionally lightweight and uses synthetic data so that the workflow can be demonstrated without exposing real financial or customer information.



Data Privacy



All invoice information used in this portfolio project is synthetic.



No real customer invoices, financial records, payment information, or personally identifiable business data are included.



Future Improvements



Potential extensions include:



PDF and image invoice input

OCR integration

Email attachment processing

Database storage

Google Sheets or CRM integration

Accounting software integration

Automatic duplicate-invoice detection

Confidence scoring

Human approval workflow

Email or Slack notification for review cases

Project Status



Status: Complete / Frozen



The core n8n workflow has been tested successfully with both complete and incomplete invoice scenarios.



The workflow is currently frozen as the portfolio version.



Project: AI Invoice / Document Data Extractor

Platform: n8n

Input: Invoice/document text

Output: Structured JSON

Validation: Automated validation + human-review fallback

Data: Synthetic portfolio data

