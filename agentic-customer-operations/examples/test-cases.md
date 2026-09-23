# Validation Cases

## 1. Grounded RAG Response

**Input:** Shipping time to Mendoza.

**Expected behavior:**
- Manager routes to DOCUMENTATION.
- RAG retrieves institutional documentation.
- Response includes available source metadata.
- QA Supervisor evaluates the response before dispatch.

## 2. Missing Knowledge

**Input:** Student discount policy.

**Expected behavior:**
- Manager routes to DOCUMENTATION.
- Knowledge base contains insufficient evidence.
- RAG returns `No sé`.
- QA blocks autonomous dispatch.
- Case is escalated to human review.

## 3. Lead Qualification

**Input:** Customer expresses purchase intent and urgency but provides no defined budget.

**Expected behavior:**
- Manager routes to LEAD_QUALIFICATION.
- Worker produces an internal commercial classification.
- Internal classification is not exposed to the customer.
- Customer receives a neutral follow-up confirmation.
