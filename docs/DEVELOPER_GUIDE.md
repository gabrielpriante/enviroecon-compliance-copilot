# Developer Quick Reference

## Essential Reading (Start Here)

1. **[README.md](../README.md)** - Project overview and core principles
2. **[docs/GUARDRAILS.md](GUARDRAILS.md)** - Non-negotiable defensive design rules
3. **[CONTRIBUTING.md](../CONTRIBUTING.md)** - How to contribute effectively
4. **[docs/ARCHITECTURE.md](ARCHITECTURE.md)** - System design and components

## Core Principles (Know These by Heart)

### 1. Citation Integrity Above All
- Every regulatory statement needs a verified citation
- No secondary source reliance
- 100% verification required

### 2. Zero Hallucination Tolerance
- Never generate unverified regulatory content
- Flag uncertainty explicitly
- Fail safely when verification impossible

### 3. Explicit Assumptions Everywhere
- Log all assumptions in structured format
- Document in dedicated output sections
- Never make silent assumptions

### 4. Defensibility Over Helpfulness
- Incomplete + accurate > complete + uncertain
- Conservative interpretation when ambiguous
- Recommend professional review liberally

## Before You Code

### Pre-Development Checklist
- [ ] Read GUARDRAILS.md thoroughly
- [ ] Understand citation standards (CITATION_STANDARDS.md)
- [ ] Review system architecture (ARCHITECTURE.md)
- [ ] Check roadmap for feature alignment (ROADMAP.md)
- [ ] Read contribution guidelines (CONTRIBUTING.md)

### Feature Development Checklist
- [ ] Feature aligns with core principles
- [ ] Guardrails maintained or enhanced
- [ ] Citation verification planned
- [ ] Assumption tracking designed
- [ ] Error handling defined
- [ ] Tests written (TDD preferred)
- [ ] Documentation prepared

## Common Development Patterns

### Adding Regulatory Analysis
```python
# Pattern: Conservative interpretation with assumption tracking

def analyze_requirement(regulation_text, context):
    # 1. Verify citation
    citation = verify_citation(regulation_text.citation)
    if not citation.verified:
        raise VerificationError("Cannot verify citation")
    
    # 2. Document assumptions
    assumptions = AssumptionTracker()
    assumptions.add("factual", "Assuming federal jurisdiction applies")
    
    # 3. Apply conservative interpretation
    requirement = interpret_conservatively(regulation_text)
    
    # 4. Flag uncertainty
    if requirement.has_ambiguity():
        requirement.flag_for_professional_review()
    
    # 5. Return with full audit trail
    return {
        'requirement': requirement,
        'citation': citation,
        'assumptions': assumptions.list(),
        'confidence': 'medium',  # Triggers professional review
        'alternatives': consider_alternative_interpretations()
    }
```

### Citation Verification
```python
# Pattern: Multi-layer citation verification

def verify_citation(citation_string):
    # Layer 1: Format validation
    if not validate_citation_format(citation_string):
        return CitationResult(verified=False, reason="Invalid format")
    
    # Layer 2: Source verification
    source = query_authoritative_database(citation_string)
    if not source.exists:
        return CitationResult(verified=False, reason="Source not found")
    
    # Layer 3: Content verification
    if not source.text_matches(expected_content):
        return CitationResult(verified=False, reason="Content mismatch")
    
    # Layer 4: Currency check
    if source.is_superseded():
        return CitationResult(verified=False, reason="Regulation superseded")
    
    return CitationResult(verified=True, source=source, timestamp=now())
```

### Hallucination Prevention
```python
# Pattern: Grounding in verified sources

def generate_analysis(query):
    # 1. Retrieve only verified regulatory sources
    sources = retrieve_verified_sources(query)
    
    # 2. Ground AI generation in retrieved sources
    analysis = ai_analyze(
        query=query,
        sources=sources,
        mode="conservative",
        require_citations=True
    )
    
    # 3. Verify all claims against sources
    for claim in analysis.claims:
        if not verify_claim_against_sources(claim, sources):
            raise HallucinationDetected(f"Unverified claim: {claim}")
    
    # 4. Cross-reference check
    cross_verify(analysis, authoritative_database)
    
    return analysis
```

## Testing Patterns

### Guardrail Tests
```python
# Pattern: Test that guardrails block violations

def test_hallucination_detection():
    """System must reject hallucinated regulations"""
    fake_citation = "40 C.F.R. § 999.99 (fake regulation)"
    
    with pytest.raises(HallucinationDetected):
        system.generate_memorandum(
            query="What does 40 C.F.R. § 999.99 require?",
            allow_unverified=False  # Guardrail enforced
        )

def test_assumption_tracking():
    """All assumptions must be logged"""
    result = system.analyze("Is this facility subject to RCRA?")
    
    # Must have logged assumptions about jurisdiction, facility type, etc.
    assert len(result.assumptions) > 0
    assert result.has_assumption_section()
```

### Citation Tests
```python
# Pattern: Verify citation accuracy

def test_citation_verification():
    """All citations must verify against authoritative sources"""
    memorandum = system.generate_memorandum(test_query)
    
    for citation in memorandum.citations:
        result = verify_citation(citation)
        assert result.verified == True
        assert result.source_url is not None
        assert result.source_accessible == True
```

## Common Pitfalls to Avoid

### ❌ Don't Do This
```python
# ❌ Vague regulatory reference
analysis = "Environmental regulations require permits..."

# ❌ Unverified citation
citation = generate_plausible_citation(topic)

# ❌ Silent assumption
if user_facility_in_california:  # Assumed without documentation
    apply_california_rules()

# ❌ Liberal interpretation without disclosure
return most_permissive_interpretation()

# ❌ Legal advice language
return "You must comply with this regulation"
```

### ✅ Do This Instead
```python
# ✅ Specific, verified citation
analysis = "40 C.F.R. § 122.2 (2024) requires permits for..."

# ✅ Verified citation with link
citation = verify_and_cite("40 C.F.R. § 122.2")

# ✅ Explicit assumption logging
assumptions.add("jurisdictional", "Assuming facility located in California")
if assumed_california:
    apply_california_rules()

# ✅ Conservative interpretation with disclosure
return conservative_interpretation_with_alternatives()

# ✅ Informational language
return "The regulation states that... [professional review recommended]"
```

## Code Review Focus Areas

When reviewing code, pay special attention to:

1. **Citation Verification**: Are all citations verified?
2. **Hallucination Risk**: Could this generate unverified content?
3. **Assumption Tracking**: Are assumptions logged?
4. **Error Handling**: Does it fail safely?
5. **Conservative Mode**: Is conservative interpretation default?
6. **Test Coverage**: Are guardrails tested?

## Quick Commands (When Implemented)

```bash
# Run all tests
pytest

# Run guardrail tests only (must pass)
pytest tests/guardrails/ --strict

# Run with coverage
pytest --cov=src --cov-report=html

# Run citation verification tests
pytest tests/guardrails/test_citation_integrity.py -v

# Run hallucination detection tests
pytest tests/guardrails/test_no_hallucination.py -v

# Check code quality
flake8 src/ tests/
black --check src/ tests/
mypy src/

# Format code
black src/ tests/
```

## Getting Help

- **Questions**: Open issue with "question" label
- **Design Discussions**: Use GitHub Discussions
- **Bugs**: Use bug report template
- **Features**: Use feature request template

## Documentation Updates

When you change code, update:
- **Inline documentation**: For complex logic
- **README.md**: If behavior changes
- **ARCHITECTURE.md**: If design changes
- **GUARDRAILS.md**: If guardrails affected (rare)
- **Tests**: Always

## Key Files Reference

| File | Purpose |
|------|---------|
| `README.md` | Project overview |
| `CONTRIBUTING.md` | Contribution guide |
| `CODE_OF_CONDUCT.md` | Community standards |
| `DISCLAIMER.md` | Legal disclaimer |
| `SECURITY.md` | Security policy |
| `docs/ARCHITECTURE.md` | System design |
| `docs/GUARDRAILS.md` | Defensive design rules |
| `docs/CITATION_STANDARDS.md` | Citation requirements |
| `docs/ROADMAP.md` | Development roadmap |

## Remember

> **"When in doubt, err on the side of caution and conservative interpretation. An incomplete but accurate answer is preferable to a complete but questionable one."**

This is not just a guideline—it's the foundation of system trustworthiness.

---

*Last Updated: January 2026*
