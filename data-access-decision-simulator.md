# Data Access Decision Simulator

**Organisation:** EduConnect Ghana (50,000+ students)
**Role:** DevOps Engineer
**Policy applied:** EduConnect data classification policy (PUBLIC / INTERNAL / CONFIDENTIAL), data lifecycle (Create, Store, Use, Share, Archive, Destroy), principle of least privilege, Ghana Data Protection Act, 2012 (Act 843)

## Summary of decisions

| # | Request | Requester | Decision | Lifecycle stage |
|---|---------|-----------|----------|-----------------|
| 1 | Full student database for referral campaign | Sarah Owusu, Marketing Manager | **Conditional approval** (reduced data set, no CONFIDENTIAL fields released to Marketing) | Use |
| 2 | Direct AWS database access for DataInsights Inc. (US) | David Mensah, Head of Product | **Deny** (as submitted) | Share |
| 3 | Right-to-be-forgotten request from James Boateng | Comfort Asante, Customer Support Lead | **Approve**, with limited legally required retention | Destroy (with Archive for retained records) |

---

## Request 1: Marketing Campaign

**From:** Sarah Owusu, Marketing Manager
**Asked for:** Full student database (names, emails, phone numbers, course enrollments) for a referral campaign starting Friday
**Classification of requested data:** Emails (CONFIDENTIAL), phone numbers (CONFIDENTIAL, student PII), names (INTERNAL), enrollments (INTERNAL)
**Requester's access level:** INTERNAL

### Decision

**Conditional approval.** The request for the full database is denied. Marketing can run the campaign through a controlled route that does not hand CONFIDENTIAL data to the Marketing team.

### Lifecycle stage

**Use.** Data that was collected for delivering courses would be used for a new purpose (marketing). If the list were exported to an email or SMS vendor, it would also become **Share**.

### Justification

- **Classification policy:** Emails and phone numbers are CONFIDENTIAL. Sarah holds INTERNAL access only, so releasing them to her would break the policy regardless of how urgent the campaign is.
- **Least privilege:** A referral campaign needs a way to contact students, not a copy of the whole database. Phone numbers, full enrollment history and inactive accounts are not needed. Giving Marketing a raw export would grant far more access than the task requires.
- **Purpose limitation and consent (Act 843):** Students gave their details to take courses. Using them for marketing is a new purpose. Only students who have opted in to marketing messages should be contacted, and every message must include an opt-out.
- **Urgency is not a justification.** A Friday deadline does not change the classification of the data.

### Conditions / safeguards

1. Only students who have **opted in** to marketing communications are included.
2. Messages are sent **through the platform's own email service** (or an approved vendor under a signed data processing agreement). Marketing writes the content and chooses the audience segment; Marketing does not receive the email list.
3. **Phone numbers are excluded** entirely (not needed for a referral email).
4. If segmentation is needed, Marketing receives **aggregated INTERNAL data only** (e.g. number of active students per course), which matches Sarah's current access.
5. Every message includes an **unsubscribe link**, and opt-outs are honoured immediately.
6. Any temporary send list is **deleted after the campaign**, and the send is logged.

### Who to consult

- **Data Protection Officer / Data Protection Supervisor:** confirms the consent basis and purpose limitation.
- **Data owner for student records** (e.g. Head of Student Services or Registrar): approves the use.
- **Legal / Compliance:** if an external email vendor is used, to review the data processing agreement.
- **Sarah's line manager:** informed of the decision and the agreed approach.

### Action steps

1. Reply to the ticket: full database request denied; offer the conditional route above.
2. Ask the DPO to confirm which students have marketing consent.
3. Set up the campaign in the platform's email tool targeting opted-in students only.
4. Share the aggregated segment counts with Sarah if needed.
5. After the campaign, delete any temporary lists and record the decision and send in the access log.

---

## Request 2: Analytics Partnership

**From:** David Mensah, Head of Product
**Asked for:** Access for DataInsights Inc. (US-based) to EduConnect's AWS database to run their algorithms. Login credentials attached to the ticket.
**Classification of requested data:** Student activity logs (INTERNAL), student profiles (CONFIDENTIAL)
**Compliance note:** EduConnect is subject to the Ghana DPA (Act 843)

### Decision

**Deny** as submitted. The partnership can be reconsidered once the legal and technical controls listed below are in place.

### Lifecycle stage

**Share.** Data would be disclosed to an external third party, and transferred outside Ghana.

### Justification

- **Credentials were shared in a ticket.** This is a security incident in its own right. The credentials must be treated as compromised and rotated, whatever the decision on the partnership.
- **Least privilege:** Direct database access would expose all tables, including CONFIDENTIAL profiles, payment information and grades, when the stated purpose (analysing learning patterns) only needs activity data. A third party should never hold standing credentials to the production database.
- **Classification policy:** Student profiles are CONFIDENTIAL. CONFIDENTIAL data cannot leave the company without a documented legal basis and approval from the data owner.
- **Legal/compliance red flags (Act 843):**
  - No sign of a **data processing agreement** defining what DataInsights may do with the data.
  - **Cross-border transfer** to the US with no documented safeguards.
  - No evidence that students were told their data would be shared with a third party for analytics, or that there is another lawful basis.
  - No sign that the **Data Protection Officer** or Legal reviewed the deal before it was signed.
  - No **Data Protection Impact Assessment** for a large-scale use of student data.
  - EduConnect remains the data controller and is accountable for anything DataInsights does with the data.
- **Authority:** Signing a business partnership does not give the Head of Product the authority to approve access to CONFIDENTIAL data.

### Controls required if data is shared later

1. **No direct database access.** Provide a one-off or scheduled **extract** instead.
2. **De-identify the data:** remove names, emails, phone numbers and payment data; replace student IDs with pseudonymous keys held only by EduConnect. Share aggregated data where possible.
3. Share **only the fields needed** for learning-pattern analysis (data minimisation).
4. Deliver through a **separate, restricted location** (e.g. a dedicated S3 bucket with a time-limited, read-only IAM role scoped to that bucket), encrypted at rest and in transit.
5. **Logging** of all access (e.g. CloudTrail / S3 access logs).
6. **Retention limit** and a requirement that DataInsights deletes the data at the end of the contract and confirms it in writing.
7. **Right to audit** DataInsights' handling of the data.

### Documentation required

- Signed **Data Processing Agreement** (purpose, permitted processing, security measures, sub-processors, breach notification, deletion at end of contract).
- **Cross-border transfer assessment** and safeguards for transfer to the US.
- **Data Protection Impact Assessment**.
- Written approval from the **DPO** and the **data owner**.
- Updated **privacy notice** to students describing the sharing, if required.
- Entry in the company's register of processing activities / third-party processors.

### Action steps

1. **Immediately rotate the credentials** posted in the ticket, remove them from the ticketing system, and review database logs for any use of them.
2. Reply to David: request denied as submitted; explain the red flags and the route to approval.
3. Report the credential exposure to the Security lead and the DPO.
4. Hand the partnership to Legal and the DPO for the DPA, transfer assessment and DPIA.
5. Once approved, build the de-identified extract and restricted delivery location described above. Do not create a database user for DataInsights.

---

## Request 3: Archive & Deletion

**From:** Comfort Asante, Customer Support Lead
**Asked for:** Guidance on deleting all data for student James Boateng, who has invoked his "right to be forgotten"
**Classification of data:** All student data (CONFIDENTIAL)
**Current status:** Account inactive, last course completed 6 months ago, no outstanding payments

### Decision

**Approve.** The request can be fulfilled. Most of James's data should be deleted. A small set of records may be kept where the law requires it, and James must be told what is kept and why.

### Lifecycle stage

**Destroy.** Records that must be kept for legal reasons move to **Archive** with restricted access until their retention period ends, after which they are destroyed.

### Can the request be fulfilled?

Yes. James's account is inactive, he owes nothing, and his course ended six months ago, so there is no ongoing contract that requires his data. Under Act 843 personal data should not be kept longer than needed for its purpose, and a data subject can ask for incorrect, irrelevant, excessive or unlawfully held data to be deleted or destroyed. Keeping his full profile has no remaining purpose.

### Legal obligations under Ghana's DPA (Act 843)

- **Verify identity** before acting, so data is not deleted on someone else's request.
- Do not keep personal data **longer than necessary** for the purpose it was collected for.
- Act on the request and **inform James** of the outcome within a reasonable time.
- Keep data **secure** while it is held and destroy it **securely**.
- Where data was shared with third parties (e.g. email or payment providers), take reasonable steps to have them delete it too.

### What may be retained, and why

| Data | Keep? | Reason |
|------|-------|--------|
| Profile (name, email, phone, address), login, learning activity, messages | **Delete** | No remaining purpose |
| Grades / course progress | **Delete**, unless EduConnect issues accredited certificates that must be verifiable; if so keep only certificate ID, name, course and date | Certificate verification |
| Payment / invoice records | **Retain** for the statutory period (Ghana tax and accounting rules, typically 6 years) | Legal obligation (tax, audit) |
| Record of the deletion request (date, ticket ID, what was deleted, what was kept) | **Retain**, with minimal personal data | Proof of compliance |
| Analytics already in aggregated form | Keep | No longer personal data |

Retained records are moved to a restricted archive, access-controlled, encrypted, and destroyed when their retention period ends.

### Process for Customer Support

1. **Log** the request as a ticket and **verify identity** (e.g. a confirmation sent to the registered email address).
2. **Check for blockers:** outstanding payments, open disputes, legal holds. (None in this case.)
3. **Escalate** to the DPO / Data Protection Supervisor to confirm the retention list above.
4. **Raise a deletion task** to Engineering/DevOps to remove James's data from:
   - production database
   - file storage (uploads, certificates)
   - analytics and data warehouse tables
   - email/CRM tools and other third-party processors
   - backups: either purge, or mark for deletion so the data is not restored and expires with the backup cycle
5. **Archive** the retained payment records with restricted access and a set destruction date.
6. **Confirm in writing** to James: what was deleted, what was kept, why, and for how long.
7. **Record** the completed request in the data subject request log for audit.

### Action steps (DevOps)

1. Run the deletion across all listed systems using the student ID.
2. Verify that the data no longer appears in each system.
3. Move retained financial records to the restricted archive with an expiry date.
4. Report completion to Customer Support and the DPO.

---

## Success criteria check

| Criterion | Where it is addressed |
|-----------|----------------------|
| Correctly applied data classification rules | R1: CONFIDENTIAL fields withheld from an INTERNAL user. R2: CONFIDENTIAL profiles not shared without legal basis. R3: all CONFIDENTIAL data deleted or archived securely. |
| Identified appropriate lifecycle stages | R1: Use. R2: Share. R3: Destroy (with Archive). |
| Demonstrated understanding of least privilege | R1: send via platform, no list given to Marketing. R2: no direct DB access; scoped, time-limited, de-identified extract only. |
| Recognised compliance implications | Consent and purpose limitation (R1); DPA, cross-border transfer, DPIA and credential exposure (R2); retention limits and erasure under Act 843 (R3). |
