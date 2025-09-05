# Blocker Removal Playbook

**Purpose:**  
Ensure that blockers raised by engineers are identified quickly, categorized correctly, and removed effectively, so the team maintains momentum and focus.


## 1. Detect Blockers
- Ask directly in standups: *“Any blockers I can help clear today?”*  
- Use a `#blockers` Slack thread or JIRA “Blocked” tag for async reporting.  
- Probe during 1:1s when you hear “I’ll figure it out later.”  


## 2. Categorize the Blocker
| Category              | Examples                                   | Response Tactic |
|--|--|--|
| **People / Comm**     | Missing approvals, unclear requirements    | Connect to decision-maker, clarify requirements, escalate unresponsive stakeholders |
| **Process / Flow**    | Conflicting priorities, slow reviews        | Make a call, adjust process, align with product/leadership |
| **Technical / Tools** | Env down, missing credentials, flaky CI    | Get infra/DevOps help, escalate to platform team, unblock with temporary workaround |
| **External / Vendor** | Waiting on other team/vendor, compliance   | Escalate via manager peers, set SLA expectations, highlight systemic issue |


## 3. Act Fast, Multiply Impact
- Don’t solve everything yourself — **clear the path** so the engineer can move forward.  
- Provide a **parallel task** if the blocker can’t be cleared immediately.  
- Always communicate status back to the engineer — don’t leave them guessing.  


## 4. Prevent Recurrence
- Maintain a **Blocker Log** (simple table or doc):  
  - Date raised  
  - Owner  
  - Category  
  - Resolution time  
- Review weekly with product/design peers for recurring patterns.  
- Share top systemic blockers monthly with directors for support.  


## 5. Ritual Cadence
- **Daily:** Ask in standups.  
- **Weekly:** Review the blocker log with peers.  
- **Monthly:** Report top blockers & proposed systemic fixes upward.  


## 6. Example Slack Messages
**Raising:**  
> Blocked on API credentials for staging. Waiting on DevOps. ETA unknown. Can anyone help?  

**Responding:**  
> Got it — I’ll ping DevOps right now. If not resolved in 2 hours, we’ll pair you on Ticket #123 to keep you moving.  


## Success Metric
Engineers feel safe raising blockers and see them resolved quickly (measured in days/hours, not weeks).