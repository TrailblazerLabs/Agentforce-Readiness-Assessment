# SRA - System Readiness Assessment for Salesforce Agentforce

**Track:** Strategist
**Purpose:** A pre-flight diagnostic for whether a Salesforce org is actually ready to deploy Agentforce - and precisely where it will break if you skip the diagnosis. Run this *before* scoping Topics and Actions, not after.

---

## How to use this

1. Run the facilitation guide as a structured discovery session (workshop or 1:1 interviews) with the org's Salesforce admin/architect, a Data Cloud/data engineering contact if one exists, a business process owner, and - if governance is a live question - IT security or compliance.
2. Score each of the 7 dimensions 1–5 using the rubric.
3. Total the score (max 35) and read the band.
4. Use the "Red Flags" under each dimension as go/no-go gates - a single severe red flag can override a decent numeric score. This is a diagnostic tool, not a pass/fail gate on its own; treat the score as a conversation starter, not a verdict.

---

## Scoring Scale (applies to every dimension)

| Score | Label | Meaning |
|---|---|---|
| 1 | Not Started | No practice exists; not yet on anyone's radar |
| 2 | Ad Hoc | Exists inconsistently, undocumented, tribal knowledge |
| 3 | Defined but Unvalidated | Documented/designed but not tested under real load or edge cases |
| 4 | Managed | Consistently applied, monitored, owned by a named role |
| 5 | Optimized | Proven, automated where possible, actively improved over time |

---

## The 7 Dimensions

### 1. Data Readiness
*Agents reason over data. Bad data produces confident, wrong answers at machine speed.*

**Facilitation questions:**
- Walk me through the object(s) this agent will primarily read from and write to. When was the data model last cleaned up?
- What's your current duplicate rate on [key object]? Is dedup automated or manual?
- Is data siloed across systems that feed this process, or is there already a single source of truth?
- Who owns data quality today - is there a named owner, or is it "everyone's job" (i.e., no one's)?

**Rubric anchors:**
- **1–2:** No data quality process; known duplicate/stale record issues; no one owns it.
- **3:** Data quality rules/validation exist but aren't consistently enforced or monitored.
- **4:** Active dedup, validation rules, and a named data steward; quality is monitored.
- **5:** Data quality is instrumented with dashboards and actively maintained across all systems feeding this process.

**Red flag:** Duplicate or conflicting records in the exact object(s) the agent will act on. This alone can justify a "not ready" verdict regardless of other scores.

---

### 2. Data Cloud & Einstein Trust Layer Readiness
*This is the grounding and safety layer underneath Agentforce specifically - distinct from general CRM data hygiene, with its own licensing, configuration, and failure modes.*

**Facilitation questions:**
- Is Data Cloud already provisioned and licensed in this org, or would this be net-new? Do you know the consumption model (ingested rows, unified profiles) you're budgeting against?
- What source systems need to stream into Data Cloud to ground this agent - are those Data Streams already configured, or does this start from zero?
- Has identity resolution (matching/reconciliation rules) been set up to unify records across sources? If not, the agent will be grounding on fragmented, non-unified data.
- Are the Data Model Objects (DMOs) this agent needs to reason over already mapped, or does that mapping work still need to happen?
- Has anyone configured Einstein Trust Layer settings - data masking rules for PII/sensitive fields, zero-retention with the LLM provider, toxicity detection thresholds - or are defaults still in place, unreviewed?
- Is there an audit trail/logging plan for what data was sent to the model and what the agent returned?
- Which LLM is in play - Salesforce-hosted, or a Bring Your Own LLM (BYO-LLM) configuration - and has that decision actually been made, or is it assumed?

**Rubric anchors:**
- **1–2:** Data Cloud not provisioned or licensed; no grounding pipeline exists; Trust Layer settings are untouched defaults, not reviewed for this use case.
- **3:** Data Cloud provisioned but ingestion/unification is incomplete for the target objects; Trust Layer defaults have been reviewed but not tailored (e.g., masking rules not yet mapped to known PII fields).
- **4:** Data Streams and DMOs configured for this specific use case; identity resolution running; Trust Layer masking rules configured for known sensitive fields; consumption budget is understood and tracked.
- **5:** Full grounding pipeline live and monitored (Data Streams, DMOs, Calculated Insights where relevant); Trust Layer fully configured and periodically audited; consumption tracked against budget with alerting before overage.

**Red flag (hard blocker, not just a maturity gap):** The agent is scoped to ground on Data Cloud but Data Cloud isn't licensed or provisioned yet. **Red flag (safety-critical):** PII or other sensitive fields flow toward the model with no masking rule configured.

---

### 3. Metadata & Permission Architecture
*An agent inherits the access model you give it. A messy permission model becomes a security incident at agent speed.*

**Facilitation questions:**
- Describe your permission model: profiles, permission sets, permission set groups. Is it consolidated or sprawling?
- If this agent could see or edit anything a standard user can, would that be a problem? What *shouldn't* it touch?
- Is field-level security (FLS) actively maintained, or has it drifted from what the UI shows users?
- Do you have a dedicated integration/agent user, or would this run under a shared or admin identity?

**Rubric anchors:**
- **1–2:** No clear permission strategy; agent would likely run under an overprivileged or shared identity.
- **3:** Permission sets exist and are documented, but haven't been reviewed for agent-specific scoping.
- **4:** A dedicated, least-privilege permission set (or set group) has been designed specifically for the agent's Actions.
- **5:** Permission architecture is least-privilege by design org-wide, audited, and the agent identity is scoped and reviewed on a cadence.

**Red flag:** No plan for a dedicated agent identity - defaulting to "just use an admin-level integration user" is the single most common way this goes wrong.

---

### 4. Process & Automation Landscape
*Agent Actions have to coexist with whatever Flows, triggers, and automations already run on these objects.*

**Facilitation questions:**
- What automation already fires on the object(s) this agent will touch - Flows, Apex triggers, validation rules?
- Has anyone mapped order of execution, or is it discovered when something breaks?
- Is there Flow or trigger sprawl (duplicated logic, orphaned automations) on this object?
- If the agent creates/updates a record, what downstream automation fires as a side effect - and is that desired?

**Rubric anchors:**
- **1–2:** Automation landscape is undocumented; order of execution is unknown or a known pain point.
- **3:** Automation is inventoried but not recently audited for conflicts or recursion risk.
- **4:** Automation is documented, order of execution is understood, and there's a clear integration point for new agent Actions.
- **5:** Automation is actively governed (e.g., a documented automation strategy/standard), regularly audited, with clean separation of concerns.

**Red flag:** Known recursion or conflicting-automation issues on the target object(s) that haven't been resolved.

---

### 5. Topic & Action Design Maturity
*This tests whether the underlying business process is actually clean enough to hand to an agent - not whether Agentforce itself is configured.*

**Facilitation questions:**
- Can you describe this business process in discrete, unambiguous steps - or does it depend heavily on human judgment/exceptions?
- How many edge cases or exceptions does this process have, and are they documented anywhere?
- Is there an existing SOP, or does this process currently live in one person's head?
- What does "done correctly" look like for this process, in terms you could test against?

**Rubric anchors:**
- **1–2:** Process is undocumented tribal knowledge; heavy reliance on human judgment calls not yet articulated.
- **3:** Process is documented at a high level but edge cases aren't captured.
- **4:** Process is documented with edge cases and exception handling clearly defined - ready to map to Topics/Actions.
- **5:** Process has been decomposed into discrete, testable steps with clear success criteria; SOP exists and is maintained.

**Red flag:** The process owner cannot articulate the process without saying "it depends" more than once or twice without being able to specify on what.

---

### 6. Governance & Guardrails
*What happens when the agent is uncertain, wrong, or asked to do something it shouldn't - the organizational process around the agent, distinct from the technical Trust Layer settings in Dimension 2.*

**Facilitation questions:**
- What's the escalation path when the agent can't complete or isn't confident about a task? Who's the human backstop?
- Is there a human-in-the-loop checkpoint planned for high-risk actions (e.g., anything touching money, contracts, customer-facing comms)?
- How will you test/evaluate agent behavior before go-live - do you have a test plan (including grounded-accuracy testing), or is testing informal?
- Who monitors the agent post-launch, and what triggers a rollback?

**Rubric anchors:**
- **1–2:** No escalation plan; no human-in-the-loop design; no testing plan beyond "try it and see."
- **3:** Escalation and testing are planned/discussed but not yet documented or assigned to an owner.
- **4:** Documented escalation path, defined human-in-the-loop checkpoints for high-risk Actions, and a pre-launch test plan with an owner.
- **5:** Full governance framework: monitoring dashboards, defined rollback criteria, regular review cadence, clear accountable owner.

**Red flag:** No named human owner for post-launch monitoring - "the agent is live" with no one watching it.

---

### 7. Change Management & Organizational Readiness
*The technical build can be perfect and the rollout can still fail on adoption.*

**Facilitation questions:**
- Who's the executive sponsor, and how visible is their support to the end users who'll interact with this agent?
- How do end users currently feel about automation/AI touching this process - trust, skepticism, fear of job impact?
- Is there a training/communication plan for the humans whose workflow changes because of this agent?
- What's the plan if adoption is low three months post-launch?

**Rubric anchors:**
- **1–2:** No named sponsor; no communication plan; rollout is effectively "turn it on and see."
- **3:** Sponsor identified and communication planned, but not yet executed or tested with users.
- **4:** Active sponsorship, a communication/training plan in motion, and a way to measure adoption.
- **5:** Sponsorship, training, and adoption measurement are all active, with a feedback loop back into the agent's design.

**Red flag:** End users were not consulted or informed before this assessment is happening.

---

## Scoring Summary

| Dimension | Score (1–5) |
|---|---|
| 1. Data Readiness | |
| 2. Data Cloud & Einstein Trust Layer Readiness | |
| 3. Metadata & Permission Architecture | |
| 4. Process & Automation Landscape | |
| 5. Topic & Action Design Maturity | |
| 6. Governance & Guardrails | |
| 7. Change Management & Org Readiness | |
| **Total (out of 35)** | |

## Readiness Bands

| Score | Band | Recommendation |
|---|---|---|
| 7–16 | **Not Ready** | Foundational gaps exist. Address data, Data Cloud/licensing, permissions, or process documentation before scoping any Topics/Actions. |
| 17–24 | **Emerging** | Pilot-ready with tight guardrails. Choose a narrow, low-risk process for a first Topic; do not attempt broad deployment yet. |
| 25–31 | **Ready** | Scoped deployment is reasonable. Proceed with the process(es) that scored strongest; keep monitoring guardrails from Dimension 6 in place. |
| 32–35 | **Optimized** | Org is well-positioned to scale Agentforce across multiple processes with confidence. |

**Note:** A single Red Flag under any dimension warrants a conversation before proceeding, even in a "Ready" or "Optimized" overall band - the numeric score doesn't override a live security, licensing, or data-integrity risk. The Dimension 2 hard-blocker flags (unlicensed Data Cloud, unmasked PII) are non-negotiable regardless of total score.

---

## Next Steps After Scoring

- **Not Ready:** Prioritize the two lowest-scoring dimensions as the immediate remediation backlog before any Agentforce configuration work begins. If Dimension 2 scored low because Data Cloud isn't provisioned, that's usually the critical-path item - grounding can't happen without it.
- **Emerging:** Select the single business process with the highest Dimension 5 (Topic/Action Maturity) score as your pilot candidate - this is your best shot at an early win.
- **Ready / Optimized:** Move to Topic/Action design and mapping, using Dimension 5's process decomposition as your starting input, with Dimension 2's grounding pipeline already in place to support it.

---

## Appendix: Estimating Your Consumption Budget

*Pricing mechanics as of Q3 2026. Salesforce changes these models regularly (Agentforce alone has shifted pricing structure multiple times since its 2024 launch) - treat the mechanics below as durable, but confirm current list prices with your account executive or the official pricing page before presenting numbers to a client.*

**Data Cloud runs on a single fungible consumption credit.** You buy credits (list price around $500 per 100,000 credits) and spend them across ingestion, identity unification, segmentation, activation, and agent grounding - Salesforce consolidated what used to be several separate credit types into one. Two things changed recently that matter for scoping: ingesting *structured data already native to Salesforce* (from Sales Cloud, Service Cloud, etc.) is now free, so your credit spend is mostly driven by external/non-Salesforce sources and by unification and grounding activity, not raw ingestion volume. Storage is billed separately (roughly $23/TB/month) rather than out of the credit pool. A limited free tier exists (capped around 10,000 unified profiles, no segmentation/activation) - useful for a proof-of-concept, not for a production grounding pipeline.

**Agentforce itself is priced under one of three models, and an org has to pick one - Flex Credits and per-conversation pricing cannot run in the same org simultaneously:**

- **Flex Credits** (consumption-based): drawn from the same $500/100k credit pool as Data Cloud. A standard agent action costs about 20 credits (~$0.10); a voice action costs about 30 credits (~$0.15). This model rewards efficient agent design - fewer, more targeted Actions per interaction.
- **Conversations** (fixed-fee): about $2 per conversation, where a "conversation" is a session window (up to 24 hours) rather than a single message. Simpler to forecast, but a one-line deflected FAQ and a twenty-turn back-and-forth cost the same - and this model doesn't reward tightly-scoped Topics/Actions the way Flex Credits does.
- **Per-user licensing**: a flat per-seat fee (roughly $125–$550/user/month depending on tier - the higher end corresponds to full Agentforce 1 Editions) that replaces consumption billing for employee-facing agents. Orgs on this tier or an Agentforce add-on typically get unlimited internal-agent usage instead of metering per action.

**Rule of thumb for the facilitation session:** if the client's average agent interaction involves more than ~20 discrete actions, per-conversation pricing tends to be cheaper; below that, Flex Credits tends to be cheaper. This is exactly why Dimension 5 (Topic & Action Design Maturity) matters financially, not just architecturally - a bloated, poorly-scoped Topic doesn't just perform worse, it costs more under Flex Credits.

**Budgeting reality check:** Data Cloud is very often the line item clients underestimate. Entry-level paid tiers commonly start in the tens of thousands of dollars annually before any Agentforce consumption is added on top, and premium add-ons (dedicated Data Spaces, real-time profile activation, cross-org data sharing) are priced separately again. When you present a readiness score, pair it with a rough range, not a false-precision number - actual cost depends heavily on data volume, number of source systems, and action design, all of which are exactly what Dimensions 1, 2, and 5 are designed to surface.

**What to ask before quoting anything to a client:**
- Which consumption model are they already contracted under (or evaluating) - Flex Credits, Conversations, or per-user?
- Is Data Cloud already licensed, or is this a net-new purchase alongside Agentforce?
- Do they have Digital Wallet visibility turned on yet (Salesforce's native usage-tracking tool) so consumption can be monitored against budget from day one rather than discovered at renewal?

---

*Part of the Trailblazer Labs Strategist track - Agentforce readiness tooling.*
