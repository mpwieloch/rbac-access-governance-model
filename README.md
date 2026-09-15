# RBAC Access Governance Model
### Wieloch Health Center (Fictional Organization) — Access Control & Least Privilege Design

**Author:** Mitchell P. Wieloch

**Purpose:** Personal project demonstrating identity and access governance methodology, built to complement hands-on IAM administration experience.

---

## 1. Overview

This project simulates the design of a role-based access control (RBAC) framework for a
fictional 20-employee healthcare organization, Wieloch Health Center. The goal was to
move beyond day-to-day account administration (provisioning, deprovisioning, password resets)
and demonstrate the governance layer that sits on top of it: defining who *should* have access
to what, identifying where access violates least privilege or creates segregation-of-duties
(SoD) risk, and designing a repeatable review process to catch drift over time.

A healthcare org was chosen deliberately — it introduces a real compliance driver (HIPAA
"minimum necessary" standard) alongside the more universal SoD and least-privilege concerns
found in any industry.

**Deliverables:**

- [`RBAC_Access_Governance_Matrix.xlsx`](./RBAC_Access_Governance_Matrix.xlsx) — role-to-system access matrix with flagged violations, severity ratings, and legend
- This README — methodology, findings, and remediation process

---

## 2. Methodology

### Step 1 — Defined roles and departments
Identified 8 representative roles spanning HR, IT, Finance, Sales, Clinical, and Compliance —
enough diversity to surface realistic conflicts without the matrix becoming unwieldy.

### Step 2 — Mapped access per role

For each role, documented:

- **System/application** the role needs access to
- **Access level** (read, read/write, admin, approval rights)
- **Business justification** for that access

### Step 3 — Applied least privilege and rated severity
Reviewed each row against the principle of least privilege, marking each as **Compliant**,
**Non-Compliant**, or **Review Required**, and assigned every row a severity rating (**Low**,
**Medium**, **High**, **Critical**) based on the sensitivity of the system and the potential
impact if that access were misused — not just whether a violation was found. This means even
compliant access is rated (e.g., Domain Admin is rated High severity because of what it can
do, even though the access itself is justified), while flagged violations were rated by how
much immediate risk they introduce. Intentionally modeled realistic failure patterns commonly
found in real environments:

| Pattern | Example in Matrix |
|---|---|
| **Segregation of duties conflict** | Finance Analyst can both submit and approve their own transactions |
| **Over-provisioned / stale access** | Sales Rep retained Finance System admin rights from a prior role change that was never revoked |
| **Excessive scope** | Clinical user has org-wide patient record access instead of unit-scoped access (HIPAA minimum-necessary violation) |
| **Standing privileged access** | IT Systems Admin holds permanent Global Admin instead of just-in-time elevation |
| **Missing lifecycle controls** | Contractor account has no expiration date tied to the engagement end date |

### Step 4 — Designed remediation and review process
For every flagged item, documented a specific remediation action rather than a generic
"revoke access" note — see Section 4.

---

## 3. Segregation of Duties (SoD) Analysis

Two SoD conflicts were identified in the matrix:

1. **Finance Analyst — submit + approve transactions.** A single user should never be able to
   both create and approve the same financial transaction. Remediation: remove approval rights
   from the analyst role; route all approvals to Finance Manager.
2. **IT Admin — standing payroll access.** Access retained from a cross-training initiative
   eight months prior with no offboarding step when the initiative ended. Remediation: revoke
   immediately; payroll access restricted to HR/Finance roles only, with any future cross-training
   access granted as time-bound and logged.

**Root cause common to both:** neither conflict was caught at the time of granting — both
surfaced only during periodic review. This is the core argument for recurring access
recertification rather than one-time provisioning checks.

---

## 4. Remediation Actions Summary

Severity was assigned to every access grant based on the sensitivity of the system and
potential impact if that access were exploited or misused — not just whether a rule was
technically violated. The four flagged (Non-Compliant or Review Required) findings below
were rated High or Critical since they present immediate risk to sensitive systems or
introduce a compliance exposure.

| Finding | Status | Severity | Remediation | Owner |
|---|---|---|---|---|
| IT Systems Admin — standing Global Admin | Review Required | Critical | Migrate to PIM / just-in-time elevation | IT Security |
| Finance Analyst — approve own transactions (SoD) | Non-Compliant | Critical | Remove approval rights | Finance Manager |
| Sales Rep — Finance System admin (over-provisioned) | Non-Compliant | Critical | Revoke; audit logs for unauthorized use | IT Security |
| Nurse — org-wide EHR access (excessive scope) | Non-Compliant | Critical | Reconfigure role template to unit-based scoping | Clinical IT |
| IT Support Contractor — no account expiration | Review Required | High | Enforce auto-expiration matching contract end date | IT Help Desk |
| IT Admin — stale payroll access (SoD) | Non-Compliant | High | Revoke access | IT Security |

**Prioritization approach:** Critical-severity findings were remediated first (within days of
discovery), since they represent live exposure on sensitive systems (finance, patient records,
tenant-wide identity administration). High-severity findings were addressed on a slightly
longer timeline as control/process fixes rather than emergency access removals.

---

## 5. Joiner / Mover / Leaver (JML) Process

To prevent the "stale access" pattern from recurring, access changes should be triggered by
HR lifecycle events rather than relying on manual requests:

- **Joiner:** Account provisioned with role-based access template only (no ad hoc grants at
  onboarding) — any exception requires manager + IT Security approval and is logged with a review date.
- **Mover:** Role change triggers automatic review of *all* existing access — old-role access
  is removed by default, not left in place "just in case." This directly prevents the Sales
  Rep and IT Admin findings above.
- **Leaver:** Account disabled within a defined SLA (e.g., same business day for involuntary
  terminations), with access removal confirmed — not just the account disabled while group
  memberships remain active.

---

## 6. Periodic Access Review Process

Recommended cadence: **quarterly** for standard access, **monthly** for privileged/admin roles.

1. Compliance Officer or IT Security exports current access matrix from source systems
2. Managers certify their direct reports' access is still required (attestation)
3. Any "no longer needed" or unattested access is revoked automatically after a defined grace period
4. Findings and actions logged (see `Access Review Log` tab in the workbook) for audit trail

This closes the loop — access isn't just granted correctly once, it's continuously verified.

---

## 7. Key Takeaways / What This Demonstrates

- Ability to design an access control framework, not just administer individual accounts
- Understanding of least privilege, SoD, and lifecycle-driven access management
- Familiarity with compliance-driven access requirements (HIPAA minimum necessary)
- Risk-based prioritization — rating findings by severity and sequencing remediation accordingly,
  rather than treating every violation the same
- A repeatable governance process (review cadence, ownership, remediation tracking) rather
  than a one-time cleanup

---

*This is a self-directed project using a fictional organization and simulated data — no real
client, employer, or individual data was used.*
