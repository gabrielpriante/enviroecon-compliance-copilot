# JSON Schemas for EnviroEcon Compliance Copilot

This directory contains JSON Schemas that formally define and validate the contracts specified in the documentation.

## Purpose

These schemas provide **machine-readable validation** of the mandatory contracts defined in:
- [OUTPUT_CONTRACT.md](../../docs/OUTPUT_CONTRACT.md) - 8-section compliance memorandum structure
- [INPUT_CONTRACT.md](../../docs/INPUT_CONTRACT.md) - 5-step user input wizard specification

## Schemas

### `memorandum_output_schema.json`

**Purpose**: Validates that all system-generated compliance memoranda conform to the NON-NEGOTIABLE 8-section structure.

**Based on**: [OUTPUT_CONTRACT.md](../../docs/OUTPUT_CONTRACT.md)

**Validates**:
- ✓ All 8 required sections present in correct order
- ✓ Document metadata complete (ID, timestamp, version)
- ✓ Every regulatory statement has verified citation with URL
- ✓ All assumptions categorized (factual, interpretive, jurisdictional, temporal)
- ✓ Conservative interpretation applied and documented
- ✓ Language compliance (no legal advice language)
- ✓ All required disclaimers present
- ✓ Professional review recommendations included
- ✓ Limitations explicitly disclosed

**Usage Example** (Python with jsonschema):
```python
import json
from jsonschema import validate

# Load schema
with open('config/schemas/memorandum_output_schema.json') as f:
    schema = json.load(f)

# Load memorandum output
with open('output/memorandum.json') as f:
    memorandum = json.load(f)

# Validate
try:
    validate(instance=memorandum, schema=schema)
    print("✓ Memorandum conforms to OUTPUT_CONTRACT")
except Exception as e:
    print(f"✗ Validation failed: {e}")
```

### `user_input_schema.json`

**Purpose**: Validates that all user inputs are complete, valid, and properly structured through the 5-step wizard.

**Based on**: [INPUT_CONTRACT.md](../../docs/INPUT_CONTRACT.md)

**Validates**:
- ✓ All required fields in each step completed
- ✓ Conditional field requirements met
- ✓ Data types and formats correct (dates, emails, etc.)
- ✓ Selection values from allowed enums
- ✓ Text length constraints
- ✓ Cross-field consistency and dependencies
- ✓ Acknowledgment of limitations provided

**Usage Example** (Python with jsonschema):
```python
import json
from jsonschema import validate

# Load schema
with open('config/schemas/user_input_schema.json') as f:
    schema = json.load(f)

# Load user input
with open('input/user_query.json') as f:
    user_input = json.load(f)

# Validate
try:
    validate(instance=user_input, schema=schema)
    print("✓ User input conforms to INPUT_CONTRACT")
except Exception as e:
    print(f"✗ Validation failed: {e}")
```

## Schema Versioning

These schemas are versioned with the contracts they implement:

- **Current Version**: 1.0
- **Effective Date**: 2024-01-15
- **Status**: MANDATORY - NON-NEGOTIABLE

Changes to schemas must:
1. Correspond to changes in contract documents
2. Maintain backward compatibility when possible
3. Be documented with version updates
4. Include migration guidance if breaking changes

## Integration Points

### Output Validation

**MUST**: All system-generated memoranda MUST be validated against `memorandum_output_schema.json` before delivery to user.

**Failure Handling**: If validation fails:
1. Log validation error with details
2. Alert system administrators
3. DO NOT deliver invalid output to user
4. Return error message requesting system review

### Input Validation

**SHOULD**: User inputs SHOULD be validated against `user_input_schema.json` at multiple points:
1. Real-time validation during wizard progression (per-field)
2. Final validation before submission (complete input)
3. Pre-processing validation before analysis begins

**Failure Handling**: If validation fails:
1. Provide clear, specific error message to user
2. Indicate which field(s) are invalid and why
3. Provide guidance on how to correct
4. Allow user to revise and resubmit

## Validation Layers

### Layer 1: Structural Validation (Schema)
JSON Schema validates structure, data types, required fields, and basic constraints.

**Examples**:
- All 8 sections present
- Document ID matches pattern
- Dates in ISO 8601 format
- Citations array not empty

### Layer 2: Semantic Validation (Application Logic)
Application validates semantic requirements beyond schema structure.

**Examples**:
- All citation URLs are accessible (HTTP check)
- No prohibited language patterns present (regex)
- Conservative interpretation applied (logic check)
- Citations verified against authoritative sources (API check)

### Layer 3: Quality Validation (Manual Review)
Human review of sample outputs for quality and contract compliance.

**Examples**:
- Professional tone maintained
- Analysis is coherent and logical
- Assumptions are reasonable
- Disclaimers are appropriately placed

## Testing

### Schema Validation Testing

**Positive Tests** (should pass):
```json
{
  "test_name": "complete_valid_memorandum",
  "input": "test/fixtures/valid_memorandum.json",
  "expected": "validation_pass"
}
```

**Negative Tests** (should fail):
```json
{
  "test_name": "missing_section_2",
  "input": "test/fixtures/invalid_missing_section.json",
  "expected": "validation_fail",
  "error_message_contains": "section_2_question_presented"
}
```

### Test Fixtures

Test fixtures should include:
- ✓ Complete valid memorandum
- ✓ Complete valid user input
- ✗ Missing required sections
- ✗ Invalid citation format
- ✗ Missing acknowledgment
- ✗ Prohibited language patterns
- ✗ Incomplete assumptions

## Schema Maintenance

### When to Update Schemas

Update schemas when:
1. Contract documents are updated (OUTPUT_CONTRACT.md or INPUT_CONTRACT.md)
2. New validation requirements are identified
3. Field definitions change
4. New failure modes are discovered

### Update Process

1. Propose schema change with rationale
2. Update corresponding contract document
3. Update schema with version increment
4. Test schema against fixtures
5. Update documentation
6. Deploy with backward compatibility check

### Backward Compatibility

**MUST Maintain** when possible:
- Required field definitions (don't remove required fields)
- Enum values (don't remove allowed values)
- Field names (don't rename without migration)

**MAY Change**:
- Add new optional fields
- Add new enum values
- Relax constraints (make more permissive)
- Add new validation patterns

**MUST NOT** without major version:
- Remove required fields
- Rename fields
- Change data types
- Remove enum values
- Make constraints more restrictive

## Validation Tools

### Recommended JSON Schema Validators

**Python**:
- `jsonschema` package (recommended)
- `fastjsonschema` (faster validation)

**JavaScript/Node.js**:
- `ajv` (Another JSON Schema Validator)
- `jsonschema` package

**Command Line**:
- `ajv-cli` (npm install -g ajv-cli)
- `check-jsonschema` (pip install check-jsonschema)

**Online**:
- https://www.jsonschemavalidator.net/
- https://jsonschemalint.com/

### Example Validation Script

```python
#!/usr/bin/env python3
"""
Validate memorandum output or user input against schemas
"""
import json
import sys
from jsonschema import validate, ValidationError

def validate_file(schema_path, data_path):
    """Validate data file against schema"""
    try:
        with open(schema_path) as f:
            schema = json.load(f)
        
        with open(data_path) as f:
            data = json.load(f)
        
        validate(instance=data, schema=schema)
        print(f"✓ {data_path} is valid")
        return True
        
    except ValidationError as e:
        print(f"✗ Validation failed: {e.message}")
        print(f"  Path: {' -> '.join(str(p) for p in e.path)}")
        return False
    except Exception as e:
        print(f"✗ Error: {e}")
        return False

if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: validate.py <schema.json> <data.json>")
        sys.exit(1)
    
    success = validate_file(sys.argv[1], sys.argv[2])
    sys.exit(0 if success else 1)
```

## Contract Compliance

These schemas are part of the mandatory contract compliance system. See:

- [OUTPUT_CONTRACT.md](../../docs/OUTPUT_CONTRACT.md) for complete output requirements
- [INPUT_CONTRACT.md](../../docs/INPUT_CONTRACT.md) for complete input requirements
- [GUARDRAILS.md](../../docs/GUARDRAILS.md) for system guardrail principles

**Remember**: Schema validation is necessary but not sufficient. Full contract compliance requires semantic validation and human oversight.

---

**Document Control**:
- **Version**: 1.0
- **Last Updated**: 2024-01-15
- **Status**: MANDATORY
