# Golden Sample Coding Rubric (Bill C-12 One-Year Bar Project)

You are coding Canadian refugee decisions (RPD = Refugee Protection Division first-instance decisions; RAD = Refugee Appeal Division appeal decisions; RLLR = published RPD decisions from the Refugee Law Lab Reporter; FC = Federal Court judicial review decisions). For each assigned case, read the FULL decision text file, then answer the questions below **based only on the text of the decision (or reasonable inference from it)**.

**FC cases have an extra gating question (Question 0.5) and adjusted definitions — see the "FC (Federal Court) cases" section at the end. For RPD/RAD/RLLR cases, ignore Question 0.5 entirely.**

## Question 1 — Timing of claim (`timing`)

Was the refugee claim made more than one year after the **principal claimant's first entry to Canada**?

- `a` — CLEAR (on the text or by reasonable inference) that the claim was made MORE than one year after the principal claimant's FIRST entry to Canada.
- `b` — CLEAR that the claim was made ONE YEAR OR LESS after first entry.
- `c` — unclear.

Notes:
- "First entry" means the first time the principal claimant entered Canada (any status — visitor, student, worker, prior visit), not the most recent entry.
- Reasonable inference is allowed: e.g., decision says claimant arrived March 2018 and signed a Basis of Claim in June 2018 → `b`. Arrived 2015, claimed 2019 → `a`.
- If the decision gives an arrival date and a claim/referral date, compute the gap. If only vague statements exist ("some time after arriving"), use `c`.
- For RAD decisions, the question is still about when the underlying claim was made relative to first entry (facts are usually recited in the appeal decision).

## Question 2 — Outcome (`outcome`)

Was the **principal claimant** clearly successful or unsuccessful?

- `a` — successful: obtained refugee protection (Convention refugee or person in need of protection); or, at the RAD, the appeal was ALLOWED — including where the RAD sets aside and substitutes protection AND where the RAD refers the matter back to the RPD for redetermination (that counts as success).
- `b` — unsuccessful: refugee protection denied; or, at the RAD, appeal dismissed / RPD decision confirmed against the claimant. (For Minister's appeals at the RAD: code from the claimant's perspective — if the Minister's appeal succeeds and protection is vacated/denied, that is `b`; if the Minister's appeal is dismissed and the claimant keeps protection, that is `a`.)
- `c` — unclear (e.g., interlocutory ruling, outcome not stated, split/mixed outcome for the principal claimant that can't be resolved).

## Question 3 — Basis of the claim (`basis`)

Did the claim clearly involve **allegations** of persecution based on:

- `a` — gender-based persecution (e.g., domestic/intimate-partner violence, forced marriage, FGM, honour-based violence, sexual violence, gender-specific social norms/dress codes, persecution of women as a particular social group) — and NOT sexual-orientation-based persecution.
- `b` — sexual orientation, gender identity, or gender expression (SOGIE) based persecution — and NOT gender-based persecution.
- `c` — BOTH (a) and (b) present (intersecting allegations, possibly across principal claimant and dependants).
- `d` — another form of persecution only (political opinion, religion, ethnicity, criminal gangs, blood feuds, etc.), where none of (a)/(b)/(c) apply.
- `e` — unclear what type of persecution was alleged.

Notes:
- The issue is what was ALLEGED, not what the tribunal found credible or established.
- Consider allegations concerning the principal claimant AND any dependants/associated claimants in the same decision.
- A claim by a woman is not automatically gender-based; the alleged harm or risk must be gender-based (as opposed to, e.g., a purely political or ethnic claim).

## FC (Federal Court) cases

FC cases are judicial reviews and can concern many topics beyond refugee determinations (immigration visas, detention, citizenship, H&C, PRRA, intellectual property, tax, etc.). They are coded as follows.

### Question 0.5 — Refugee JR gate (`jr_refugee`, FC only)

Is this a judicial review of a refugee determination by the Refugee Protection Division and/or the Refugee Appeal Division of the Immigration and Refugee Board?

- `a` — yes: the decision under review is an RPD or RAD refugee determination (including RPD/RAD decisions on vacation or cessation of refugee protection, and RAD dismissals of appeals from the RPD).
- `b` — no: the decision under review is something else (e.g., PRRA, H&C, visa refusal, detention review, deferral of removal, citizenship, non-immigration matters) — even if the applicant's refugee history is mentioned in passing.
- `c` — unknown: cannot tell from the text what decision is under review.

**Only where `jr_refugee` is `a` do you answer Questions 1–3.** Where it is `b` or `c`, set `timing`, `outcome`, and `basis` (and their evidence fields) to `null` in the JSON.

### Adjustments to Questions 1–3 for FC cases (when `jr_refugee` = `a`)

- **Question 1 (timing)**: unchanged in substance — was the underlying refugee claim made more than one year after the principal claimant's first entry to Canada? Use the facts recited in the FC decision.
- **Question 2 (outcome)**: code the outcome of the JUDICIAL REVIEW from the REFUGEE CLAIMANT's perspective:
  - `a` — the claimant succeeds: the application for judicial review is granted/allowed (the RPD/RAD decision is set aside and the matter returned for redetermination) where the claimant is the applicant; or, where the MINISTER is the applicant, the Minister's application is dismissed (the claimant keeps their favourable determination).
  - `b` — the claimant is unsuccessful: the claimant's application for judicial review is dismissed; or the Minister's application against a favourable determination is granted.
  - `c` — unclear (e.g., outcome not stated, interlocutory rulings, certified-question or costs rulings with no disposition of the JR itself).
- **Question 3 (basis)**: unchanged — code the alleged basis of the underlying refugee claim (allegations, not findings), considering the principal claimant and any dependants.



For EACH assigned case, write a JSON file to `data/labels/<case_id>.json`.

RPD/RAD/RLLR cases — exactly this shape:

```json
{
  "case_id": "000_RAD",
  "timing": "a",
  "timing_evidence": "one sentence: key dates/facts from the text supporting the code",
  "outcome": "b",
  "outcome_evidence": "one sentence quoting/paraphrasing the disposition",
  "basis": "d",
  "basis_evidence": "one sentence on the alleged persecution"
}
```

FC cases — the same fields plus `jr_refugee` first:

```json
{
  "case_id": "200_FC",
  "jr_refugee": "a",
  "jr_refugee_evidence": "one sentence identifying the decision under review",
  "timing": "a",
  "timing_evidence": "...",
  "outcome": "b",
  "outcome_evidence": "...",
  "basis": "d",
  "basis_evidence": "..."
}
```

Where `jr_refugee` is `b` or `c`, keep all six timing/outcome/basis fields but set each to `null`.

Allowed values: jr_refugee ∈ {a,b,c} (FC only); timing ∈ {a,b,c}; outcome ∈ {a,b,c}; basis ∈ {a,b,c,d,e}. Lowercase single letters only. Evidence sentences must be grounded in the decision text.
