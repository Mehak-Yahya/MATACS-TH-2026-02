# AI Proposal Triage Design

## 1. AI vs. rules. Which steps in your flow use AI, and which are plain code or rules? Why did you draw the line there? 

I would use AI to extract relevant information from uploaded contractor proposals, such as licence details, insurance, experience, price, timeline, warranties, and team. The AI would return structured data with supporting evidence and identify missing information.

I would use ordinary application code for mandatory pass and fail checks, weight validation, score calculations, ranking, and status assignment. These tasks need predictable and consistent results. AI can interpret documents, but it should not decide eligibility or make the final decision.



## 2. Consistency and explainability. How do you make scores repeatable and explainable? If the same proposals were ranked twice, would the result be the same? How would a reviewer see why a proposal ranked where it did? 

The system would use administrator defined criteria, weights, thresholds, and scoring rules stored in a configuration. The application would calculate weighted scores using the same deterministic formula for every eligible proposal evaluated under that configuration. Each result would include the proposal's score, criterion level breakdown, extracted evidence, and reason for its position. The same proposal data and configuration version should produce the same score and ranking.

If a weighted field is missing or unclear, the proposal would be flagged for clarification rather than assigned an invented value or automatically given zero. Its final score and rank would remain null until the required information is resolved.

## 3. Human in the loop. Where does a person review, confirm or override the result? What gets logged for audit?

A reviewer would check uncertain extractions, missing information, and disputed mandatory requirements. Confirmed mandatory failures would make a proposal ineligible, while unverified requirements would remain flagged for review. After scoring, a human reviewer would examine the provisional ranking, verify evidence, and record any override with a reason. The authorized decision maker would make the final award decision.

The audit log would record the proposal identifier, extracted evidence, configuration version, criterion scores, final calculation, status changes, reviewer actions, override reasons, timestamps, and approval decision.


## 4. Changing criteria. What happens if an admin edits the criteria or weights after some proposals have already been scored? 

Administrators would manage criteria, weights, mandatory requirements, and scoring rules through the interface rather than changing application code. When an administrator edits the configuration, the system would validate the new settings, including whether weighted criteria total 100%. A valid change would be saved as a new immutable configuration version, preserving the previous version for audit purposes.

The system would identify affected proposals and re evaluate them consistently under the new version. If a changed criterion requires information that was not previously extracted, the system would request re extraction or human review. The reviewer would see the previous and revised results, including why scores or eligibility changed, before approving the updated ranking. All proposals in a ranking must use the same configuration version.

## Risks. What could go wrong with this tool (for example wrong extraction, made-up facts, bias, manipulation, cost), and how does your design guard against each one?

- Incorrect extraction: Store supporting evidence and page references where available. Flag low confidence or ambiguous fields for human verification.

- Hallucinated information: Instruct AI to use only the proposal's contents and return null for missing values. Never treat unsupported claims as confirmed facts.

- Prompt injection:  Treat proposal contents as untrusted data. Ignore embedded instructions that attempt to change evaluation rules or manipulate rankings.

- inconsistent scoring: Use documented criteria, fixed weights, deterministic calculations, and consistent rules across proposals. Keep human overrides transparent and auditable.

- Missing information: Flag unresolved mandatory or weighted fields. Do not silently invent values or finalize incomplete rankings.

- Configuration errors: Validate new rules and weights before activation, preserve previous versions, and re-evaluate affected proposals under one consistent version.

- Cost: Use AI only for document interpretation and structured extraction, avoid unnecessary repeated calls, and use ordinary code for calculations and validation.

The system would support a transparent, repeatable evaluation process while keeping human reviewers responsible for verification and the final award decision.