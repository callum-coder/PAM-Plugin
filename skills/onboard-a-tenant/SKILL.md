---
name: onboard-a-tenant
description: Run a new tenant onboarding in PAM from "I have a new tenant" to keys handed over — collecting terms and applicants, approving terms, referencing decisions, deposit protection and handover. Use when the landlord mentions a new tenant, a tenancy starting, referencing, a deposit scheme, or moving someone in.
---

Use the `pam` connector's tools. One onboarding per property; `list_onboarding_cases` shows what is open and what PAM is waiting for.

1. **Start**: on "I have a new tenant for 12 Example Road", call `start_onboarding` with the property and everything already said (applicant names and mobiles, start date, rent, deposit, guarantor, pets, furnishing, bills). It returns what is still missing — ask for at most two things per message. Nothing is sent to anyone at this stage.
2. **Fill in**: add or correct details with `update_onboarding` (case id from the start result). Dates are YYYY-MM-DD; rent and deposit are pounds; the deposit legal cap is five weeks' rent and PAM rejects more.
3. **Gate A — approve terms**: when the result says `ready_for_approval`, read the summary back and ask for a clear yes. Only then call `onboarding_decision` with `approve_terms` and `confirmed: true`. PAM creates the pending tenancy and texts the applicants their application link.
4. **Referencing**: after the checks, relay the landlord's decision with `approve_applicant`, `needs_guarantor` or `decline` (approve and decline need a yes, then `confirmed: true`).
5. **Money and deposit**: `money_received` when the first rent and deposit arrive (starts the 30-day deposit clock); add the scheme, custodial/insured and reference with `update_onboarding`, then `deposit_protected`.
6. **Handover**: `handover_done` once keys are handed over. PAM makes the tenancy live and welcomes the tenant.

`withdraw` stops an onboarding (needs a yes); `resume` continues a parked one; `recheck` re-runs the compliance gate after certificates are renewed.

Keep replies short: what PAM recorded, and the next thing it needs.
