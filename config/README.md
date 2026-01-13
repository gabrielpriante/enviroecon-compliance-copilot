# Configuration Files and Schemas

This directory contains configuration files and JSON schemas for the EnviroEcon Compliance Copilot system.

## JSON Schemas (Mandatory Validation)

### `schemas/memorandum_output_schema.json`
JSON Schema for validating compliance memorandum outputs against the mandatory 8-section structure defined in [OUTPUT_CONTRACT.md](../docs/OUTPUT_CONTRACT.md).

**Purpose**: Ensures all system-generated memoranda conform to the NON-NEGOTIABLE output contract.

**Validates**:
- All 8 required sections present and complete
- Citation verification and URL requirements
- Assumption documentation and categorization
- Conservative interpretation application
- Disclaimer and professional review requirements
- Language compliance (no legal advice language)

### `schemas/user_input_schema.json`
JSON Schema for validating user inputs through the mandatory 5-step wizard defined in [INPUT_CONTRACT.md](../docs/INPUT_CONTRACT.md).

**Purpose**: Ensures all user inputs are complete, valid, and properly structured.

**Validates**:
- All required fields in each step
- Conditional field requirements
- Data types and format constraints
- Allowed values for selections
- Cross-field consistency

## Configuration Files (To Be Implemented)

### `system_config.example.yaml`
System-wide configuration including:
- AI model settings
- Citation verification parameters
- Guardrail thresholds
- Logging configuration

### `regulatory_sources.example.yaml`
Configuration for regulatory data sources:
- EPA API endpoints
- State regulatory databases
- Citation verification services
- Update schedules

### `output_templates.example.yaml`
Memorandum output templates:
- Standard compliance memorandum
- Summary analysis format
- Citation formatting preferences
- Disclaimer text

## Usage

1. Copy example files and remove `.example` extension
2. Update with your specific configuration values
3. Never commit actual configuration with API keys or secrets
4. See `.gitignore` for files excluded from version control

## Security Notes

- **Never commit actual API keys or credentials**
- Use environment variables for sensitive configuration
- Validate all configuration inputs
- Maintain separate configs for development, testing, and production
