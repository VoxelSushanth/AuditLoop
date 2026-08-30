# AuditLoop
Reconciliation agent for Razorpay settlements, bank statements, and internal ledgers. Deterministic matching engine runs first; an LLM only explains and proposes fixes for unresolved exceptions, never commits a match directly. Every decision is logged, and accuracy is measured against a known ground-truth batch not demoed on cherry picked examples.
