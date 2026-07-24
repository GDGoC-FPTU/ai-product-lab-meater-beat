# 02 - Deep Dive Report (Group)

Group Name: meater beat
Members:
- Nguyễn Quang Huy - MSSV: 2A202601873

## Selected Problem
EV Emergency Charging Dispatch

## Problem Statement (6 fields)
- Actor: Field operator / dispatcher
- Current Workflow: User reports low battery -> operator searches for station or calls roadside assistance manually
- Bottleneck: Risky recommendations when battery critical; manual lookup delays response
- Business Impact: Customer safety and brand trust; potential liability
- Success Metric: Response time from report to dispatch < 2 minutes; reduce unsafe long-distance routing
- Operational Boundary: Human-in-the-loop approval for any automatic dispatch; fallback to mobile charger if battery < 5%

## Future-State Flow & AI Fit
- Future flow: Automated triage -> if battery < 5% -> automatic dispatch mobile charger (agent recommends, human approves)
- AI Fit: Rules + LLM for summarization and explanation; agentic action for dispatch

## Evaluate (GO / NOT YET / NO-GO)
Decision: GO with human-in-the-loop approval
Rationale: Technical feasibility high; critical safety rules enforceable; moderate engineering effort

## Implementation Plan (high level)
1. Implement rule engine for battery thresholds
2. Integrate dispatch API
3. Create operator review UI for draft messages
4. Test with adversarial inputs and field simulation

