# Project Constitution

## Strategic Capture Intelligence Engine - Identity
* **Role**: System Pilot (deterministic, self-healing).
* **Primary Mission**: 
  1. Detect early-stage federal opportunities
  2. Match strategically aligned contractors
  3. Score and prioritize deterministically
  4. Track performance outcomes
  5. Generate actionable intelligence
  6. Continuously evolve via controlled feedback (no random auto-updates to logic).
* **Guiding Principles**:
  * Reliability > AI.
  * Data > speculation.
  * Determinism > automation hype.
  * Evolution via memory, not randomness.

## Data Schemas (Source of Truth)

### `opportunities`
```json
{
  "id": "uuid",
  "notice_id": "string (unique)",
  "title": "string",
  "description": "text",
  "agency": "string",
  "organization_code": "string",
  "naics_code": "string",
  "psc_code": "string",
  "set_aside_code": "string",
  "notice_type": "string",
  "posted_date": "timestamp",
  "response_deadline": "timestamp",
  "place_of_performance_state": "string",
  "estimated_value": "numeric",
  "priority_flag": "boolean",
  "raw_json": "jsonb",
  "created_at": "timestamp"
}
```

### `contractors`
```json
{
  "id": "uuid",
  "company_name": "string",
  "uei": "string",
  "cage_code": "string",
  "naics_codes": "string[]",
  "certifications": "string[]",
  "state": "string",
  "company_size_bracket": "string",
  "website": "string",
  "email": "string",
  "phone": "string",
  "sam_registered": "boolean",
  "last_activity_date": "timestamp",
  "raw_json": "jsonb",
  "created_at": "timestamp"
}
```

### `matches`
```json
{
  "id": "uuid",
  "opportunity_id": "uuid (fk)",
  "contractor_id": "uuid (fk)",
  "score": "numeric",
  "classification": "string (HOT | WARM | COLD)",
  "score_breakdown": "jsonb",
  "created_at": "timestamp"
}
```

### `capture_outcomes`
```json
{
  "opportunity_id": "uuid (fk)",
  "contractor_id": "uuid (fk)",
  "submitted": "boolean",
  "won": "boolean",
  "loss_reason": "string",
  "bid_hours_spent": "numeric",
  "margin_estimate": "numeric",
  "internal_notes": "text",
  "closed_date": "timestamp"
}
```

### `agency_intelligence_logs`
```json
{
  "week_start": "date",
  "top_naics": "jsonb",
  "top_agencies": "jsonb",
  "certification_performance": "jsonb",
  "win_rate_by_score_band": "jsonb",
  "competition_trends": "jsonb",
  "generated_at": "timestamp"
}
```

## Behavioral Rules & AI Constraints
* **AI Usage**: Strictly limited to summaries, email drafts, and intelligence narrative reports (e.g., Gemini Flash or Claude fallback).
* **AI Prohibition**: AI must NEVER modify business logic, scoring weights, classification thresholds, or automatically delete data.
* **Evolution**: The system identifies patterns and suggests changes. All evolution requires manual confirmation.
* **Performant Code Rules**:
  * Use batched inserts.
  * No blocking AI calls in main loops.
  * Use indexed queries (GIN indexes for arrays).
  * Pre-calculate scores; Cache NAICS mapping.

## Deliverable Payload (Portals)
1. **Internal Portal**: Dashboard (ops counts, HOT matches, backfill trigger, intelligence), Opportunity Details.
2. **Contractor Portal**: Matched ops, HOT/WARM labels, certification leverage, suggestion actions.
3. **Weekly Intelligence Report**: Win rates, agency behaviors, density shifts.

## Architectural Invariants (A.N.T.)
* **1. Architecture (`architecture/`)**: Technical SOPs. Update SOPs before updating code.
* **2. Navigation**: Logic routing between SOPs and Tools. No complex tasks directly, call atomic tools instead.
* **3. Tools (`tools/`)**: Deterministic Python scripts. Atomic and testable.
* **Environments**: Variables/tokens stored in `.env`. Use `.tmp/` for intermediates.
* **Self-Annealing**: Analyze stack trace -> Patch Tool -> Test -> Update Architecture.
* `gemini.md` is law. Planning files are memory.

## Trigger Pipeline (Execution Schedule)
* **Daily at 02:00 UTC:** Trigger `python tools/1_ingest_sam.py` (Fetches last 2 days of opportunities).
* **Daily at 03:00 UTC:** Trigger `python tools/2_score_matches.py` (Calculates deterministic scores).
* **Daily at 04:00 UTC:** Trigger `python tools/3_generate_email_drafts.py` (Drafts emails for newly generated HOT matches).
* **Weekly (Sunday at 00:00 UTC):** Trigger `python tools/4_log_outcome.py` and Intelligence workflows.

## Maintenance Log (Self-Annealing Record)
*When a Tool fails or an error occurs, the fix MUST be logged here before architecture is modified.*

| Date | Script Failed | Error Detected | Architecture SOP Updated | Fix Deployed |
| :-- | :--- | :--- | :--- | :--- |
| *YYYY-MM-DD* | *script.py* | *Short description* | *sop.md changed* | *Code fix summary* |
