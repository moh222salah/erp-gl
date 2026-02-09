# Frappe / ERPNext Customization Strategy
Production-first technical decisions for real-world ERPNext implementations.

This document explains *how and why* customization decisions were made
across ERPNext production systems, with a strong focus on accounting,
data integrity, and long-term maintainability.

---

## 1. Server Scripts vs Custom Apps
### Why Server Scripts were preferred in many cases

**Decision Principle:**  
> *Use the lightest possible customization that satisfies the business requirement
without compromising auditability or upgrade safety.*

### When Server Scripts are used
Server Scripts are chosen when:
- The requirement is **reporting, validation, or automation logic**
- Logic is **closely tied to business rules**
- No new DocTypes or schema changes are required
- The customization must remain **upgrade-safe**
- Speed of delivery matters (production urgency)

**Examples:**
- General Ledger row expansion logic
- Automated validation before Journal Entry submission
- Runtime calculation of balances or metrics
- ZATCA compliance validation hooks

**Why this works in production:**
- No app deployment overhead
- No migration risks
- Fully reversible
- Easy to audit and review
- Safe during ERPNext upgrades

---

### When Custom Apps are used
Custom Apps are used only when:
- New DocTypes are required
- Long-term feature ownership is needed
- UI extensions go beyond client scripting
- Multiple modules depend on shared logic

**Trade-off awareness:**
Custom Apps introduce:
- Version compatibility concerns
- Migration responsibility
- Higher operational overhead

This is avoided unless clearly justified.

---

## 2. SQL vs ORM (frappe.get_all / frappe.db)
### Performance-first decision making

**Rule of thumb:**
> ORM for correctness and safety  
> SQL for performance-critical financial reports

---

### Using ORM when:
- Data volume is small to medium
- Business logic clarity matters more than speed
- Permissions and filters must be automatically enforced
- Write operations are involved

**Benefits:**
- Safer
- More readable
- Less fragile across versions

---

### Using SQL when:
- Reporting on **large ledgers**
- Aggregations span millions of rows
- Real-time financial reports are required
- ORM would cause N+1 queries or timeouts

**Safeguards when using SQL:**
- Read-only queries only
- Explicit filters
- Indexed fields only
- No mutation queries
- Always wrapped inside controlled methods

**Example mindset:**
> “Financial reports must load in seconds, not minutes.”

---

## 3. Data Integrity & Audit Safety
Accounting systems are **legal records**, not just data tables.

### Core principles applied:
- No historical mutation of submitted documents
- All calculations are derived, not stored
- Original voucher data is never altered
- ERPNext’s accounting flow is respected

---

### How integrity is preserved:
- Calculations happen at runtime
- All transformations are transparent
- Source vouchers remain the single source of truth
- Full traceability from GL row → Voucher → Line item

This ensures:
- Audit readiness
- Legal compliance
- Zero reconciliation discrepancies

---

## 4. Production-First Engineering Mindset
### Design decisions are guided by operational reality, not theory.

**Key considerations:**
- Can this survive 500k+ GL rows?
- Can finance teams understand it?
- Can auditors trace it?
- Can it survive an ERPNext upgrade?

---

### What is intentionally avoided:
- Over-engineering
- Premature abstractions
- Heavy dependencies
- Complex stateful logic
- Storing computed financial values

---

### What is prioritized:
- Deterministic behavior
- Predictable performance
- Simple rollback paths
- Business transparency
- Long-term maintainability

---

## 5. Final Philosophy
ERPNext customization is not about writing more code.
It is about:
- Respecting accounting principles
- Understanding business workflows
- Protecting data integrity
- Delivering clarity to decision makers

**Good ERP systems disappear.  
Bad ERP systems create daily pain.**

This approach aims for the former.
