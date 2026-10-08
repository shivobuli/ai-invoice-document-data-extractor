AI Invoice / Document Data Extractor — Testing

1\. Testing Objective



The purpose of testing is to verify that the AI Invoice / Document Data Extractor can:



Accept invoice text through the webhook.

Extract available invoice information into structured JSON.

Preserve missing information as null.

Extract multiple invoice line items.

Validate the extracted invoice data.

Return complete invoices through the normal response path.

Route incomplete invoices to the human-review path.



All tests use synthetic portfolio data.



2\. Test Environment



Platform: n8n Cloud



Workflow: AI Invoice Document Data Extractor



Input method: HTTP POST webhook



AI model: GPT-5 mini through n8n Gateway credits



Testing tool: PowerShell



Data: Synthetic invoice/document text



3\. Test Input Format



The webhook expects a POST request containing:



{

&#x20; "document": "invoice document text"

}



The workflow test endpoint used during development was:



https://itsdigiera.app.n8n.cloud/webhook-test/ai-invoice-extractor



The test endpoint is intended for workflow testing and should not be treated as a permanent production API endpoint.



4\. Test Case 1 — Complete Invoice

Objective



Verify that a complete invoice can be converted into structured JSON.



Input



The synthetic invoice contained:



Invoice number

Invoice date

Due date

Vendor

Customer

Two line items

Currency

Subtotal

GST

Total amount

Payment status

Payment terms

Expected Result



The AI Agent should extract all available fields.



The validation condition should evaluate as true because the invoice contains a positive total amount.



Actual Result



PASS



The workflow successfully extracted:



Invoice number: INV-2026-0017

Invoice date: 06 October 2026

Due date: 20 October 2026

Currency: INR

Subtotal: 35000

Tax: 6300

Total amount: 41300

Payment status: Unpaid



It also successfully extracted both invoice line items and the vendor/customer information.



The workflow returned the structured invoice JSON through the normal response path.



5\. Test Case 2 — Incomplete Invoice

Objective



Verify that incomplete invoice information does not automatically pass validation.



Input



The synthetic test invoice contained:



Invoice number

Invoice date

Vendor name

Customer name

One line item

Quantity

Unit price



It intentionally omitted:



Due date

Subtotal

Tax

Total amount

Payment status

Payment terms

Expected Result



Missing fields should be represented as null.



Because total\_amount is missing, the validation condition should evaluate as false.



The workflow should route the document to the human-review path.



Actual Result



PASS



The AI Agent successfully returned the available information while leaving unavailable fields empty/null.



The validation branch routed the document to:



Human Review Required



The final response included:



{

&#x20; "total\_amount": null,

&#x20; "review\_status": "Human review required"

}



This confirms that the workflow can distinguish between a complete extraction and an incomplete document.



6\. Validation Test



The workflow uses the following validation condition:



$json.output.total\_amount > 0

TRUE Condition



A positive extracted total amount sends the document to the normal response path.



FALSE Condition



A missing or invalid total amount sends the document to the human-review path.



This provides a deterministic validation layer after AI extraction.



7\. Structured Output Test



The Structured Output Parser was tested with both complete and incomplete invoice documents.



The initial restrictive schema caused incomplete test data to fail because several fields could not be populated.



The schema was subsequently adjusted to permit null values for fields that may be absent from a document.



After this change, the incomplete invoice test completed successfully.



This demonstrates the importance of designing structured-output schemas to account for real-world document variability.



8\. Human Review Test



The Human Review Required node was tested with an incomplete invoice.



The node preserves the extracted invoice information and adds:



review\_status: Human review required



The final response was then configured to combine the extracted invoice object with the review status.



Result



PASS



The PowerShell response showed the extracted fields together with:



review\_status : Human review required

9\. Test Commands

Complete Invoice



The complete synthetic invoice was loaded from the project input directory and submitted using PowerShell.



$document = Get-Content "D:\\AI-Projects\\AI-Invoice-Document-Data-Extractor\\input\\sample-invoice.txt" -Raw



Invoke-RestMethod -Method POST `

&#x20; -Uri "https://itsdigiera.app.n8n.cloud/webhook-test/ai-invoice-extractor" `

&#x20; -ContentType "application/json" `

&#x20; -Body (@{document=$document} | ConvertTo-Json -Compress)

Incomplete Invoice



The incomplete invoice was submitted directly as a PowerShell multiline string.



The test intentionally omitted the total amount and several other fields to trigger the validation fallback.



10\. Test Summary

Test	Expected Result	Result

Complete invoice extraction	Structured invoice returned	PASS

Multiple line items	All available items extracted	PASS

Missing fields	Missing values represented as null/empty	PASS

Positive total validation	Normal response path	PASS

Missing total validation	Human-review path	PASS

Review status	Human review required returned	PASS

Structured output	Valid JSON structure	PASS

11\. Known Limitations



The current testing covers text-based invoice documents.



It does not test:



PDF extraction

Image/OCR extraction

Handwritten invoices

Poor-quality scans

Multi-page documents

Duplicate invoice detection

Currency conversion

Accounting-system integration

Automated human notifications



These are outside the current portfolio scope.



12\. Testing Conclusion



The AI Invoice / Document Data Extractor successfully passed the planned functional tests.



Both the normal extraction path and the human-review fallback path were verified.



The workflow is therefore considered functionally complete and frozen for the current portfolio version.



The project should not be interpreted as production-grade accounting or financial software. Additional validation, security, document parsing, monitoring, and integration controls would be required before production deployment.

