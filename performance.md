here## Performance Benchmarks

The following benchmarks are based on real production environments
with high-volume accounting data.

### Dataset Characteristics
- General Ledger Rows: 300k – 800k+
- Journal Entries: 50k+
- Concurrent Finance Users: 10–25
- Database: MariaDB (Indexed)

---

### Report Performance

| Operation                            | Avg Time |
|-------------------------------------|----------|
| Standard ERPNext GL Report           | 6–9 sec  |
| Enriched GL Intelligence Report      | 1.2–1.8 sec |
| Voucher-level expansion (runtime)    | <300 ms  |
| Month-end GL review                  | ↓ ~70% time |

---

### Optimization Techniques Applied
- Read-only SQL for heavy aggregations
- Proper indexing on posting_date, voucher_type, voucher_no
- Batched queries (no N+1)
- Runtime computation (no stored financial derivatives)
- Cached metadata, uncached financials

---

### Why Runtime > Stored Calculations
- Stored calculations risk data inconsistency
- Runtime logic guarantees:
  - Accuracy
  - Audit traceability
  - Upgrade safety


---



### Scalability Notes
- Linear performance degradation
- No exponential query growth
- Safe for month-end closing scenarios

> Performance optimization in ERP systems is not about speed alone.
> It is about delivering **predictable performance under financial pressure**. 
