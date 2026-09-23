# Agentic Customer Operations System

A modular AI automation system for customer operations, built around a Manager–Worker architecture with retrieval-augmented generation, persistent context, factual quality control, human escalation and end-to-end traceability.

## Business Problem

Customer operations teams spend significant time handling repetitive questions, qualifying commercial opportunities and searching internal documentation.

This project explores how these tasks can be automated while maintaining explicit controls against unsupported answers, duplicate processing and unsafe autonomous actions.

## Architecture

The system uses a modular Manager–Worker pattern:

- **Manager:** classifies incoming requests and delegates them to specialized workers.
- **Inventory Worker:** handles product availability and pricing queries.
- **Lead Qualification Worker:** evaluates commercial opportunities without exposing internal scoring to customers.
- **RAG Worker:** answers institutional questions using indexed documentation only.
- **QA Supervisor:** evaluates candidate responses before final dispatch.
- **Human-in-the-Loop:** blocks low-confidence or unsupported responses and escalates them for review.

## Key Features

- Manager–Worker orchestration
- Retrieval-Augmented Generation (RAG)
- Source-grounded institutional responses
- AI-as-a-Judge quality gate
- Human escalation for rejected cases
- Persistent conversational context
- Idempotent request processing
- End-to-end `trace_id`
- Controlled error outputs
- Separation between internal decisions and customer-facing responses

## Validation Examples

The system was tested with scenarios including:

1. **Document-grounded shipping query** → retrieved institutional evidence → QA approved.
2. **Information outside the knowledge base** → abstained with `"No sé"` → blocked and escalated to human review.
3. **Commercial lead qualification** → internal lead classification → customer received a neutral follow-up message instead of the internal score.

## Repository Structure

`workflows/` contains sanitized workflow exports.

`docs/` contains architecture and validation material.

`examples/` contains representative test cases.

## Security

This public repository contains no production credentials, API keys, access tokens or customer data.

Credential references and infrastructure identifiers have been replaced with placeholders.

## Disclaimer

This is a portfolio and educational implementation based on a fictional company and synthetic business data. Production deployment would require environment-specific security, monitoring, access control and compliance review.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
