# System Guardrails

## Purpose

This document defines the defensive design guardrails that ensure EnviroEcon Compliance Copilot maintains its core principles of accuracy, defensibility, and conservative interpretation. These guardrails are **mandatory** and must be preserved across all development.

## Core Guardrail Principles

### 1. Citation Integrity Above All
**Principle**: Every regulatory statement must be traceable to an authoritative source.

**Implementation Requirements**:
- ✅ Every regulation mentioned has a complete, verified citation
- ✅ Citations link directly to authoritative source documents
- ✅ Citation format follows standard legal citation conventions
- ✅ Citations include effective date or version information
- ❌ No paraphrasing of regulations without direct citation
- ❌ No "regulations typically require" without specific citation
- ❌ No generic regulatory references ("environmental laws generally...")

**Verification**:
- Automated citation format validation
- Link verification to authoritative sources
- Manual review of citation accuracy in testing

### 2. Zero Hallucinated Regulations
**Principle**: The system must never invent, fabricate, or misrepresent regulatory requirements.

**Implementation Requirements**:
- ✅ All regulatory text sourced directly from authoritative databases
- ✅ Verification against official regulatory sources before output
- ✅ Explicit flagging when regulation cannot be verified
- ✅ Conservative response when regulatory text is ambiguous
- ❌ No generation of plausible-sounding but unverified regulations
- ❌ No "filling in gaps" with common sense assumptions about regulations
- ❌ No citation of regulations that cannot be directly verified

**Detection Mechanisms**:
- Cross-reference checking against known regulatory databases
- Hallucination detection prompts in AI layer
- Rejection of outputs with unverifiable claims
- Test suite with known non-existent regulations (should be caught)

### 3. Explicit Assumptions Everywhere
**Principle**: All interpretive choices, assumptions, and limitations must be explicitly documented.

**Implementation Requirements**:
- ✅ Every assumption logged in structured format
- ✅ Assumptions compiled in dedicated output section
- ✅ Uncertainty clearly flagged in analysis
- ✅ Alternative interpretations noted where applicable
- ❌ No silent assumptions about facts or jurisdiction
- ❌ No implicit interpretive choices
- ❌ No hidden limitations

**Assumption Categories**:
1. **Factual Assumptions**: "Assuming facility is located in State X..."
2. **Interpretive Assumptions**: "Interpreting 'discharge' to include..."
3. **Jurisdictional Assumptions**: "Assuming federal jurisdiction applies..."
4. **Temporal Assumptions**: "Based on regulations effective as of [date]..."
5. **Procedural Assumptions**: "Assuming standard permit application process..."

**Output Format**:
```markdown
## Assumptions and Limitations

### Factual Assumptions
- Assumption 1: Description and rationale
- Assumption 2: Description and rationale

### Interpretive Choices
- Choice 1: Issue, interpretation chosen, alternatives considered

### Limitations
- Limitation 1: What this analysis does NOT cover
- Limitation 2: What requires additional research
```

### 4. Defensibility Over Helpfulness
**Principle**: When faced with uncertainty, prioritize defensible accuracy over complete answers.

**Implementation Requirements**:
- ✅ Incomplete but accurate analysis preferred over complete but uncertain
- ✅ Clear disclosure of analysis boundaries
- ✅ Recommendation for professional review when appropriate
- ✅ Conservative interpretation when ambiguity exists
- ❌ No stretching analysis beyond what can be defended
- ❌ No speculation to provide complete answer
- ❌ No minimizing limitations to appear more helpful

**Decision Tree**:
```
Is regulatory requirement clear and verifiable?
├─ YES → Include with full citation
└─ NO → Is conservative interpretation possible?
    ├─ YES → Include with assumption disclosure and professional review recommendation
    └─ NO → Flag as requiring professional analysis; do not speculate
```

### 5. Conservative Interpretation Mode
**Principle**: When multiple interpretations exist, default to the more stringent compliance requirement.

**Implementation Requirements**:
- ✅ Err on side of stricter compliance standard
- ✅ Flag when liberal interpretation might apply
- ✅ Note both interpretations and rationale for choosing conservative one
- ✅ Recommend professional review for significant interpretive choices
- ❌ No defaulting to "easier" interpretation without disclosure
- ❌ No minimizing compliance obligations
- ❌ No relying on "industry practice" over regulatory text

**Example**:
```markdown
**Regulatory Requirement**: 40 C.F.R. § 262.34(a) requires accumulation time 
limits for hazardous waste. The term "accumulation start date" is subject to 
interpretation.

**Conservative Interpretation** (Applied): Date when first waste is placed in 
container.

**Alternative Interpretation**: Date when container is full.

**Rationale**: The conservative interpretation ensures compliance even if 
regulatory authority takes stricter view. Recommend confirming with state 
environmental agency for facility-specific guidance.
```

### 6. Non-Legal Advice Boundaries
**Principle**: System output is informational research, not legal advice.

**Implementation Requirements**:
- ✅ Disclaimer on every output
- ✅ Language avoids legal conclusions ("should", "must comply", "legally required")
- ✅ Recommendation for legal review prominently placed
- ✅ Framing as "research summary" not "legal opinion"
- ❌ No "you should" or "you must" statements
- ❌ No advice on specific actions without professional review disclaimer
- ❌ No language implying attorney-client relationship

**Preferred Language Patterns**:
- ✅ "The regulation states..."
- ✅ "According to [citation], the requirement is..."
- ✅ "This analysis suggests... [subject to professional review]"
- ❌ "You must comply with..."
- ❌ "The legal requirement is..."
- ❌ "Our advice is..."

### 7. Verification Before Output
**Principle**: Multi-layered verification before any output reaches user.

**Verification Layers**:

**Layer 1: Citation Verification**
- Format check (proper legal citation format)
- Source verification (exists in authoritative database)
- Link validation (URL accessible and correct)
- Version check (current or properly dated)

**Layer 2: Content Verification**
- Regulatory text matches source document
- No hallucinated content in analysis
- All claims have citations
- No contradictions with known regulations

**Layer 3: Assumption Verification**
- All assumptions logged
- Assumption disclosure complete
- Limitations clearly stated
- Alternative views noted

**Layer 4: Guardrail Compliance**
- Conservative interpretation applied
- Non-legal advice language used
- Disclaimers present
- Professional review recommended where appropriate

**Failure Handling**:
- Any verification failure → Block output
- Log failure for review
- Return error to user with explanation
- Never suppress verification failures

### 8. Audit Trail Requirements
**Principle**: Complete traceability for defensibility.

**Required Logging**:
- All sources consulted (URLs, timestamps)
- All citations verified (results, timestamps)
- All assumptions made (category, rationale)
- All interpretive choices (issue, choice, alternatives)
- System configuration (model versions, prompts)
- User query (anonymized if needed)

**Audit Trail Output**:
```markdown
## Audit Trail

**Query Processed**: [timestamp]
**Regulatory Sources Consulted**: 
- eCFR API: [timestamp]
- [State] Environmental Regulations: [timestamp]

**Citations Verified**: 15/15 verified successfully
**Assumptions Logged**: 3 factual, 2 interpretive, 1 jurisdictional
**Models Used**: [version information]
**Conservative Interpretation Applied**: Yes
```

## Implementation Checklist

For any new feature or component:

- [ ] Citation verification implemented and tested
- [ ] Hallucination detection integrated
- [ ] Assumption tracking enabled
- [ ] Conservative interpretation mode default
- [ ] Non-legal advice language enforced
- [ ] Verification layers implemented
- [ ] Audit trail logging complete
- [ ] Guardrail compliance tests written
- [ ] Documentation updated

## Testing Guardrails

### Required Tests

**Citation Integrity Tests**:
- Test that uncited regulatory claims are rejected
- Test that malformed citations are caught
- Test that dead links are detected
- Test that non-authoritative sources are flagged

**Hallucination Prevention Tests**:
- Test with known non-existent regulations (should reject)
- Test with plausible but false regulatory citations (should reject)
- Test with ambiguous queries (should flag uncertainty, not invent)

**Assumption Tracking Tests**:
- Test that silent assumptions trigger failures
- Test that all assumption categories are captured
- Test that assumptions appear in output

**Conservative Interpretation Tests**:
- Test with ambiguous regulations (should choose stricter)
- Test that alternative interpretations are noted
- Test that professional review is recommended

**Language Boundary Tests**:
- Test that legal advice language is blocked
- Test that disclaimers are present
- Test that professional review recommendations are included

## Monitoring and Compliance

### Continuous Monitoring
- Log all guardrail checks
- Alert on verification failures
- Track assumption counts per query
- Monitor citation verification success rate

### Periodic Audits
- Manual review of sample outputs
- Citation accuracy spot-checks
- Assumption completeness review
- Guardrail compliance assessment

### Guardrail Violations

**Critical Violations** (Block release):
- Hallucinated regulations in output
- Uncited regulatory claims
- Missing disclaimers
- Legal advice language

**Major Violations** (Require fix):
- Incomplete assumption disclosure
- Failed citation verification
- Liberal interpretation without disclosure
- Missing professional review recommendations

**Minor Violations** (Log and review):
- Inconsistent citation formatting
- Incomplete audit trail
- Suboptimal language patterns

## Evolution of Guardrails

These guardrails may be refined based on:
- Real-world usage patterns
- Professional feedback
- Regulatory changes
- Technical capabilities

**Process for Guardrail Changes**:
1. Propose change with rationale
2. Assess impact on defensibility
3. Community review and discussion
4. Implementation with enhanced testing
5. Documentation update
6. Monitoring of change impact

**Immutable Principles** (Never compromise):
- Citation integrity
- Zero hallucination
- Explicit assumptions
- Non-legal advice boundaries

---

*These guardrails are the foundation of system trustworthiness. All development must maintain these standards.*
