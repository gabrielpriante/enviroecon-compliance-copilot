# Input Contract: User Input Wizard Specification

## Document Purpose

This document defines the **NON-NEGOTIABLE** 5-step user input wizard structure and requirements for the EnviroEcon Compliance Copilot system. This contract ensures complete, validated, and defensible inputs for compliance memorandum generation.

## Scope and Applicability

**MUST**: All user inputs MUST be collected through this 5-step wizard structure.

**MUST NOT**: The system MUST NOT proceed with analysis if required information is missing.

**MUST**: The system MUST request clarification rather than guessing or making assumptions about missing information.

---

## Required 5-Step Wizard Structure

### Overview

The input wizard collects information in a progressive disclosure format, with each step building on the previous. The wizard MUST:

1. Validate inputs at each step before proceeding
2. Provide clear guidance and examples
3. Request clarification for ambiguous inputs
4. Apply conservative defaults where explicitly defined
5. Fail safely when required information is missing

---

## Step 1: Regulatory Domain and Jurisdiction

**Purpose**: Identify the specific environmental compliance area and applicable jurisdictions.

### Required Fields

#### Field 1.1: Primary Regulatory Domain

**Field Name**: `regulatory_domain`

**Type**: Single selection (required)

**Allowed Values**:
- `rcra_hazardous_waste` - Resource Conservation and Recovery Act (Hazardous Waste)
- `clean_water_act` - Clean Water Act (NPDES, Wetlands, Stormwater)
- `clean_air_act` - Clean Air Act (Air Emissions, Permits)
- `cercla_superfund` - CERCLA/Superfund (Site Cleanup, Liability)
- `tsca` - Toxic Substances Control Act
- `epcra` - Emergency Planning and Community Right-to-Know Act
- `safe_drinking_water` - Safe Drinking Water Act
- `nepa` - National Environmental Policy Act
- `state_specific` - State-specific environmental program
- `other` - Other regulatory domain (requires specification)

**Validation Rules**:
- MUST select exactly one value
- MUST NOT proceed without selection
- If `other` selected, MUST provide specification in Field 1.2

**Default**: None (explicit selection required)

**Error Behavior**: If not selected, display error: "Regulatory domain selection is required. Select the primary environmental regulation applicable to your question."

**Example - Acceptable**:
```
Selected: rcra_hazardous_waste
```

**Example - Unacceptable**:
```
Selected: (none)
```
❌ **Error**: Required field not completed

#### Field 1.2: Regulatory Domain Specification (Conditional)

**Field Name**: `regulatory_domain_other`

**Type**: Free text (conditionally required)

**Required When**: Field 1.1 = `other` or `state_specific`

**Validation Rules**:
- MUST NOT be empty if Field 1.1 = `other`
- MUST be 10-500 characters
- MUST specify the actual regulatory program or statute
- MUST NOT be vague (e.g., "environmental rules")

**Error Behavior**: If Field 1.1 = `other` and this field is empty, display error: "Please specify the regulatory domain. Provide the name of the specific environmental regulation, statute, or program applicable to your question."

**Example - Acceptable**:
```
California Proposition 65 (Safe Drinking Water and Toxic Enforcement Act of 1986)
```

**Example - Unacceptable**:
```
State environmental compliance
```
❌ **Error**: Too vague; specify the actual statute or regulatory program

#### Field 1.3: Federal Jurisdiction

**Field Name**: `federal_jurisdiction`

**Type**: Boolean (required)

**Allowed Values**:
- `true` - Federal regulations apply
- `false` - Only state/local regulations apply
- `unknown` - Uncertain whether federal jurisdiction applies

**Validation Rules**:
- MUST select one value
- If `unknown`, system MUST flag uncertainty in analysis
- If `false`, Field 1.5 (State) becomes required

**Default**: `true` (conservative assumption: federal regulations apply unless explicitly excluded)

**Clarification Request**: If `unknown` selected, system displays: "Federal jurisdiction is uncertain. The analysis will conservatively assume federal regulations apply. A jurisdictional determination may be required. Professional review is recommended to confirm applicable jurisdiction."

**Example - Acceptable**:
```
Selected: true
```

#### Field 1.4: State/Territory

**Field Name**: `state_jurisdiction`

**Type**: Single selection (conditionally required)

**Required When**: 
- Field 1.3 = `true` (to check for authorized state programs)
- Field 1.3 = `false` (state regulations are primary)
- Facility location determination

**Allowed Values**: 
- All 50 U.S. states
- District of Columbia
- U.S. territories (Puerto Rico, U.S. Virgin Islands, Guam, American Samoa, Northern Mariana Islands)
- `unknown` - State/territory not specified
- `multiple` - Multi-state question (requires clarification)

**Validation Rules**:
- MUST select one value
- If `unknown`, system MUST note limitation and recommend state-specific analysis
- If `multiple`, system MUST request clarification on which state's requirements to analyze

**Default**: `unknown` (triggers limitation notice)

**Clarification Request**: 
- If `unknown`: "State jurisdiction is not specified. This analysis will address federal requirements only. State-authorized programs may impose more stringent requirements. State regulations MUST be separately reviewed. Please specify state location for comprehensive analysis."
- If `multiple`: "Multi-state analysis requested. Please specify which state's requirements should be analyzed, or request separate analyses for each state."

**Example - Acceptable**:
```
Selected: California
```

**Example - Acceptable (with clarification)**:
```
Selected: unknown
System Note: "State jurisdiction unspecified. Analysis limited to federal requirements. State review required."
```

#### Field 1.5: Local Jurisdiction Consideration

**Field Name**: `local_jurisdiction_flag`

**Type**: Boolean (optional)

**Allowed Values**:
- `true` - Local (county/municipal) regulations may apply
- `false` - Local regulations not applicable
- `unknown` - Uncertain about local requirements

**Validation Rules**:
- Optional field
- If `true` or `unknown`, system MUST note limitation that local ordinances are not analyzed

**Default**: `unknown`

**System Response**: If `true` or `unknown`, system adds to limitations: "Local (county, municipal) environmental ordinances may impose additional requirements not addressed in this analysis. Local regulatory requirements should be separately reviewed."

---

### Step 1: Validation Summary

**Before Proceeding to Step 2**:

✓ **MUST Validate**:
- [ ] Regulatory domain selected (Field 1.1)
- [ ] If "other" selected, specification provided (Field 1.2)
- [ ] Federal jurisdiction determined (Field 1.3)
- [ ] State/territory selected or `unknown` with limitation note (Field 1.4)

✓ **MUST Capture Assumptions**:
- Document if federal jurisdiction assumed (Field 1.3 = default true)
- Document if state is unknown (limitation)
- Document if local requirements may apply (limitation)

❌ **MUST NOT Proceed If**:
- Regulatory domain not selected
- "Other" selected but not specified
- Required fields incomplete

---

## Step 2: Specific Regulatory Question

**Purpose**: Capture the precise compliance question to be addressed.

### Required Fields

#### Field 2.1: Question Text

**Field Name**: `compliance_question`

**Type**: Long text (required)

**Validation Rules**:
- MUST NOT be empty
- MUST be 20-2,000 characters
- MUST be a specific question (not a broad topic)
- MUST relate to the regulatory domain specified in Step 1
- SHOULD be answerable with regulatory citations

**Quality Checks** (System MUST flag if fails):
- ✓ Question includes specific regulatory issue (e.g., "accumulation time limits" not just "waste storage")
- ✓ Question is not multi-part (if multi-part, system suggests breaking into separate queries)
- ✓ Question relates to environmental compliance (not general legal advice)
- ✗ Question is too broad ("What are all RCRA requirements?")
- ✗ Question requests legal advice ("Should we hire a consultant?")
- ✗ Question is outside system scope ("What is the tax treatment of...?")

**Clarification Requests**:

**If too broad**, system displays:
```
Your question appears broad and may be difficult to address comprehensively. 
Consider narrowing to a specific regulatory requirement. 

Example: Instead of "What are all Clean Water Act requirements?", ask "What 
are the NPDES permit requirements for stormwater discharge from a construction 
site exceeding 1 acre in California?"
```

**If multi-part**, system displays:
```
Your question contains multiple sub-questions. For the most accurate analysis, 
submit separate inquiries for each distinct regulatory issue.

Detected sub-questions:
1. [First question]
2. [Second question]

Would you like to proceed with question 1 only, or revise to a single focused question?
```

**If unclear**, system displays:
```
Your question may be ambiguous. Please clarify:
- What specific regulatory requirement or obligation is at issue?
- What is the specific activity, substance, or situation involved?
- What is the specific compliance determination needed?
```

**Default**: None (user input required)

**Error Behavior**: If empty or < 20 characters, display: "Please provide a specific compliance question. Your question should identify the regulatory issue, relevant facts, and compliance determination needed."

**Example - Acceptable**:
```
Under RCRA regulations, what are the maximum accumulation time limits for a 
large quantity generator storing ignitable hazardous waste in containers at 
an industrial facility in Ohio?
```

✅ **Quality**: Specific regulatory issue, jurisdiction identified, clear compliance question

**Example - Acceptable (with assumptions)**:
```
What are the NPDES permit requirements for stormwater discharge from a 
construction site?

System Note: Jurisdiction not specified in question. System will request 
jurisdiction information in Step 1. Assuming general federal requirements 
unless state specified.
```

✅ **Quality**: Specific issue, system identifies need for clarification

**Example - Unacceptable**:
```
Tell me about environmental compliance.
```

❌ **Error**: Too broad, not a specific question, no regulatory domain identified

**Example - Unacceptable**:
```
What should I do about waste storage and how long can I keep it and what 
permits do I need and what are the penalties?
```

❌ **Error**: Multi-part question, needs to be broken into separate queries

#### Field 2.2: Question Category

**Field Name**: `question_category`

**Type**: Single selection (required)

**Allowed Values**:
- `regulatory_interpretation` - Interpreting specific regulatory requirement
- `applicability_determination` - Whether regulation applies to situation
- `compliance_obligation` - What is required to comply
- `timeline_deadline` - When something must be done
- `exemption_qualification` - Whether exemption or exclusion applies
- `permit_requirement` - Whether permit is required
- `reporting_requirement` - Reporting, notification, or recordkeeping requirements
- `other` - Other category (requires specification)

**Validation Rules**:
- MUST select exactly one value
- If `other`, MUST provide specification

**Purpose**: Helps system structure analysis appropriately

**Default**: None (explicit selection required)

**Example - Acceptable**:
```
Selected: timeline_deadline
(Corresponds to question about "maximum accumulation time limits")
```

#### Field 2.3: Key Regulatory Citations (Optional)

**Field Name**: `known_citations`

**Type**: Multi-line text (optional)

**Format**: One citation per line

**Validation Rules**:
- Optional field
- If provided, system will prioritize these citations in research
- System MUST still independently verify citations
- System MUST NOT rely solely on user-provided citations
- Format: Standard legal citation format preferred

**Purpose**: Allows users to point system to specific regulations they're asking about

**Default**: Empty (system will identify relevant citations)

**System Behavior**: If provided, system uses as starting point but conducts independent citation verification and may identify additional relevant citations.

**Example - Acceptable**:
```
40 C.F.R. § 262.34(a)
40 C.F.R. § 260.10
```

**Example - Acceptable (incomplete citation)**:
```
40 CFR Part 262

System Note: "User referenced 40 C.F.R. Part 262. System will identify specific 
applicable sections within Part 262 for analysis."
```

---

### Step 2: Validation Summary

**Before Proceeding to Step 3**:

✓ **MUST Validate**:
- [ ] Question text provided and meets length requirements (Field 2.1)
- [ ] Question is specific and focused (not multi-part)
- [ ] Question relates to regulatory domain from Step 1
- [ ] Question category selected (Field 2.2)

✓ **MAY Request Clarification** (but can proceed):
- Question appears broad → suggest narrowing
- Question appears multi-part → suggest splitting
- Question unclear → request clarification
- User can revise or proceed with clarification noted as assumption

❌ **MUST NOT Proceed If**:
- Question text missing or too short
- Question category not selected
- Question is completely outside system scope (e.g., not environmental compliance)

---

## Step 3: Factual Context and Assumptions

**Purpose**: Collect relevant facts and operational context necessary for analysis.

### Required Fields

#### Field 3.1: Facility Type (if applicable)

**Field Name**: `facility_type`

**Type**: Single selection (conditionally required)

**Required When**: Question relates to facility-specific compliance

**Allowed Values**:
- `industrial_manufacturing` - Manufacturing or industrial facility
- `commercial` - Commercial facility (office, retail, etc.)
- `construction_site` - Construction or demolition site
- `waste_management` - Waste treatment, storage, or disposal facility
- `wastewater_treatment` - Wastewater treatment plant
- `landfill` - Landfill or disposal site
- `laboratory` - Laboratory facility
- `healthcare` - Healthcare facility
- `educational` - School or university
- `government` - Government facility
- `agricultural` - Agricultural operation
- `mining` - Mining operation
- `not_applicable` - Question not facility-specific
- `other` - Other facility type (requires specification)

**Validation Rules**:
- If question is facility-specific, selection required
- If `not_applicable`, skip facility-related fields
- If `other`, MUST provide specification

**Default**: `not_applicable`

**Clarification Request**: If uncertain, system displays: "Is your question related to a specific facility type? If yes, select the type. If no, select 'Not Applicable.'"

**Example - Acceptable**:
```
Selected: industrial_manufacturing
```

#### Field 3.2: Facility Size/Classification (if applicable)

**Field Name**: `facility_size`

**Type**: Single selection or free text (conditionally required)

**Required When**: Regulatory requirements vary by facility size/classification

**Context-Sensitive Options**:

**For RCRA Hazardous Waste** (if Step 1 domain = `rcra_hazardous_waste`):
- `large_quantity_generator` - ≥1,000 kg/month hazardous waste
- `small_quantity_generator` - 100-1,000 kg/month hazardous waste
- `very_small_quantity_generator` - <100 kg/month hazardous waste
- `unknown` - Generator status not determined

**For Clean Water Act** (if Step 1 domain = `clean_water_act`):
- `major_discharger` - Major NPDES permit holder
- `minor_discharger` - Minor NPDES permit holder
- `small_ms4` - Small Municipal Separate Storm Sewer System
- `construction_over_1_acre` - Construction site >1 acre
- `construction_under_1_acre` - Construction site <1 acre
- `unknown` - Classification not determined

**For Clean Air Act** (if Step 1 domain = `clean_air_act`):
- `major_source` - Major source (Title V)
- `area_source` - Area source
- `synthetic_minor` - Synthetic minor source
- `unknown` - Source classification not determined

**Generic** (for other domains):
- Free text describing size/classification
- `unknown` - Size/classification not determined

**Validation Rules**:
- Required if regulatory requirements are size-dependent
- If `unknown`, system MUST note as limitation and may need to analyze multiple scenarios

**Default**: `unknown` (triggers clarification and limitation)

**Clarification Request**: If `unknown`, system displays:
```
Facility size/classification not specified. Regulatory requirements may vary 
by classification. 

For [regulatory domain], classification determines:
- [List key differences]

Please specify classification if known, or system will note this limitation 
and may need to address multiple scenarios.
```

**Example - Acceptable**:
```
Selected: large_quantity_generator
Basis: Facility generates approximately 2,500 kg hazardous waste per month
```

**Example - Acceptable (with limitation)**:
```
Selected: unknown

System Note: "Generator status not specified. Analysis will address requirements 
for large quantity generators (most stringent). If facility is actually SQG or 
VSQG, different requirements may apply."
```

#### Field 3.3: Relevant Substances/Materials (if applicable)

**Field Name**: `substances_materials`

**Type**: Multi-line text (conditionally required)

**Required When**: Question relates to specific substances, materials, or waste streams

**Validation Rules**:
- If question is substance-specific, this field is required
- One substance/material per line
- Should include common name and/or chemical name
- Should include CAS number if known (optional but helpful)
- If `unknown`, MUST specify and system will note limitation

**Format**:
```
[Common Name] ([Chemical Name]) [CAS #] (optional)
```

**Default**: None

**Clarification Request**: If question appears substance-specific but field is empty:
```
Your question appears to relate to specific substances or materials. Please 
specify:
- Substance/material name(s)
- Chemical composition if known
- CAS numbers if available
- Waste stream description if applicable

If specific substances are unknown, the analysis will be generic and may not 
address substance-specific requirements or exemptions.
```

**Example - Acceptable**:
```
Toluene (Methylbenzene) CAS 108-88-3
Xylene (Dimethylbenzene) CAS 1330-20-7
Paint waste containing these solvents
```

**Example - Acceptable (general)**:
```
Ignitable hazardous waste (specific composition varies)

System Note: "Specific waste composition not provided. Analysis will address 
general requirements for ignitable hazardous waste (D001). Waste-specific 
requirements may apply depending on actual composition."
```

#### Field 3.4: Activity Description

**Field Name**: `activity_description`

**Type**: Long text (required)

**Validation Rules**:
- MUST NOT be empty
- MUST be 50-2,000 characters
- MUST describe the specific activity, operation, or situation at issue
- SHOULD include relevant operational details

**Purpose**: Provides context for applicability and compliance analysis

**Clarification Request**: If too vague, system displays:
```
Activity description is too general. Please provide specific details about:
- What is being done (specific operations, processes)
- Where it is being done (location, setting)
- How it is being done (methods, equipment)
- When it occurs (frequency, duration)
- Quantities or volumes involved

Specific details enable more accurate regulatory applicability determination.
```

**Default**: None (user input required)

**Error Behavior**: If empty, display: "Please describe the activity or situation that raises the compliance question. Include relevant operational details."

**Example - Acceptable**:
```
On-site accumulation of ignitable hazardous waste (primarily spent solvents 
from parts cleaning operations) in 55-gallon steel drums. Waste is accumulated 
in a designated outdoor storage area on a concrete pad with secondary containment. 
Approximately 8-12 drums are filled per month. Waste is picked up by licensed 
hazardous waste transporter for off-site disposal approximately quarterly.
```

✅ **Quality**: Specific operations, methods, quantities, frequency provided

**Example - Unacceptable**:
```
We store waste.
```

❌ **Error**: Too vague; lacks necessary detail about what, where, how, quantities

#### Field 3.5: Additional Relevant Facts

**Field Name**: `additional_facts`

**Type**: Multi-line text (optional)

**Validation Rules**:
- Optional field
- Should include any other facts relevant to compliance determination
- May include permit status, compliance history, existing controls, etc.

**Purpose**: Capture any other relevant context

**Default**: Empty

**Example - Acceptable**:
```
- Facility has existing RCRA Part B permit for waste treatment
- Facility is in attainment area for air quality
- No previous environmental violations or enforcement actions
- Site is not in floodplain or wetland area
```

---

### Step 3: Validation Summary

**Before Proceeding to Step 4**:

✓ **MUST Validate**:
- [ ] If facility-specific question, facility type selected (Field 3.1)
- [ ] If size-dependent requirements, size/classification specified or `unknown` noted (Field 3.2)
- [ ] If substance-specific question, substances specified or limitation noted (Field 3.3)
- [ ] Activity description provided with sufficient detail (Field 3.4)

✓ **MUST Document Assumptions**:
- Document if facility type assumed
- Document if classification unknown (limitation)
- Document if substance composition generic (limitation)
- Document all `unknown` selections as limitations

❌ **MUST NOT Proceed If**:
- Facility-specific question but facility type not specified and not `unknown`
- Activity description missing or insufficient (<50 characters)

---

## Step 4: Temporal Scope and Urgency

**Purpose**: Establish time-related aspects of the compliance question.

### Required Fields

#### Field 4.1: Regulatory Effective Date Scope

**Field Name**: `regulatory_date_scope`

**Type**: Single selection (required)

**Allowed Values**:
- `current_regulations` - Current effective regulations only
- `specific_date` - Regulations effective as of specific date (requires Field 4.2)
- `historical_analysis` - Historical regulatory requirements (requires Field 4.2)
- `future_compliance` - Upcoming regulations or future compliance dates (requires Field 4.3)

**Validation Rules**:
- MUST select exactly one value
- If `specific_date` or `historical_analysis`, MUST provide date in Field 4.2
- If `future_compliance`, MUST provide date in Field 4.3

**Default**: `current_regulations`

**Purpose**: Determines which version of regulations to analyze

**Example - Acceptable**:
```
Selected: current_regulations

System Note: "Analysis based on regulations effective as of [system verification date]."
```

**Example - Acceptable (specific date)**:
```
Selected: specific_date
Date: 2020-06-15

System Note: "Analysis based on regulations effective as of June 15, 2020. 
Regulatory changes after this date are not included. Current regulations should 
be verified separately."
```

#### Field 4.2: Historical/Specific Date (Conditional)

**Field Name**: `specific_date`

**Type**: Date (conditionally required)

**Required When**: Field 4.1 = `specific_date` or `historical_analysis`

**Validation Rules**:
- Must be valid date in format YYYY-MM-DD
- Must not be future date (for historical analysis)
- Should not be more than 20 years in past (system may lack historical regulatory data)

**Clarification Request**: If date is >20 years ago:
```
Historical date specified is [X] years ago. System regulatory database may not 
include comprehensive historical data for this period. Historical regulatory 
research by qualified professional recommended for definitive analysis.
```

**Example - Acceptable**:
```
Date: 2018-03-01
Purpose: Determining compliance obligations at time of inspection
```

#### Field 4.3: Future Compliance Date (Conditional)

**Field Name**: `future_compliance_date`

**Type**: Date (conditionally required)

**Required When**: Field 4.1 = `future_compliance`

**Validation Rules**:
- Must be valid date in format YYYY-MM-DD
- Must be future date
- Should include context about why future date is relevant

**Purpose**: Analyze upcoming regulatory requirements or deadlines

**System Behavior**: System will note if proposed rules exist but are not final, with limitation that analysis is based on current information and subject to change.

**Example - Acceptable**:
```
Date: 2025-06-01
Context: Anticipated effective date of new air emissions standards
```

#### Field 4.4: Time-Sensitive Action or Deadline

**Field Name**: `deadline_urgency`

**Type**: Single selection (optional)

**Allowed Values**:
- `immediate_action_needed` - Compliance action needed within days/weeks
- `upcoming_deadline` - Deadline within months
- `routine_planning` - Long-term planning, no immediate deadline
- `unknown` - Time sensitivity unknown
- `not_applicable` - No time-sensitive element

**Validation Rules**:
- Optional field
- If `immediate_action_needed`, system includes prominent note recommending urgent professional consultation

**Default**: `routine_planning`

**System Behavior**: 
- If `immediate_action_needed`, system displays:
  ```
  ⚠️ URGENT PROFESSIONAL CONSULTATION RECOMMENDED
  
  You have indicated an immediate compliance action is needed. This system 
  provides informational research only and cannot substitute for urgent 
  professional advice.
  
  RECOMMENDED IMMEDIATE ACTIONS:
  1. Contact qualified environmental compliance professional immediately
  2. Contact legal counsel if enforcement action is involved
  3. Contact regulatory agency for specific guidance if appropriate
  
  Do not rely solely on this analysis for time-sensitive compliance decisions.
  ```

**Example - Acceptable**:
```
Selected: upcoming_deadline
Context: Permit renewal application due in 90 days
```

#### Field 4.5: Specific Deadline Date (Conditional)

**Field Name**: `specific_deadline_date`

**Type**: Date (conditionally required)

**Required When**: Field 4.4 = `immediate_action_needed` or `upcoming_deadline`

**Validation Rules**:
- Must be valid date in format YYYY-MM-DD
- Should be future date (if past, may indicate missed deadline situation)

**Purpose**: Identify specific compliance deadline

**Example - Acceptable**:
```
Date: 2024-04-15
Context: NPDES permit renewal due
```

---

### Step 4: Validation Summary

**Before Proceeding to Step 5**:

✓ **MUST Validate**:
- [ ] Regulatory date scope selected (Field 4.1)
- [ ] If specific/historical date, date provided (Field 4.2)
- [ ] If future compliance, date provided (Field 4.3)
- [ ] If deadline indicated, date provided (Field 4.5)

✓ **MUST Flag**:
- Immediate action needed → urgent professional consultation warning
- Historical date >20 years → data limitation warning
- Future compliance → subject to change warning

❌ **MUST NOT Proceed If**:
- Specific date required but not provided
- Future compliance date required but not provided
- Deadline date required but not provided

---

## Step 5: Analysis Preferences and Confirmation

**Purpose**: Confirm analysis scope, set preferences, and obtain user acknowledgment.

### Required Fields

#### Field 5.1: Desired Analysis Depth

**Field Name**: `analysis_depth`

**Type**: Single selection (required)

**Allowed Values**:
- `comprehensive` - Detailed regulatory analysis with all applicable requirements
- `focused` - Focused analysis of specific regulatory question only
- `citation_summary` - Primary regulatory citations and brief summary only

**Validation Rules**:
- MUST select exactly one value
- Affects length and detail of analysis, but all outputs must meet 8-section contract

**Default**: `focused`

**Implications**:
- `comprehensive`: May include related requirements, cross-references, compliance strategies
- `focused`: Addresses specific question presented only
- `citation_summary`: Minimal analysis, emphasis on citations and brief interpretation

**System Note**: All analysis depths include complete 8-section memorandum structure. "Citation summary" option reduces analysis depth but maintains all required sections.

**Example - Acceptable**:
```
Selected: focused

System Note: "Analysis will address the specific question presented. Related 
regulatory requirements may be noted but not analyzed in detail unless directly 
relevant to the question."
```

#### Field 5.2: Include State Regulatory Research (Conditional)

**Field Name**: `include_state_research`

**Type**: Boolean (conditionally required)

**Required When**: Step 1, Field 1.4 (state jurisdiction) is specified (not `unknown`)

**Allowed Values**:
- `true` - Include state-specific regulatory research
- `false` - Federal regulations only (state requirements noted as limitation)

**Validation Rules**:
- If state specified in Step 1, user must choose
- If `false`, system MUST note state requirements as significant limitation

**Default**: `true` (if state specified)

**System Behavior**:
- If `true`: System will research state-specific regulations
- If `false`: System notes: "State-specific regulations not analyzed. State requirements may be more stringent than federal and MUST be separately reviewed."

**Limitation**: System capability to research state regulations may be limited. Even if `true`, system may note state research limitations.

**Example - Acceptable**:
```
Selected: true
State: California

System Note: "Analysis will include California-specific regulations. California 
has EPA-authorized RCRA program with more stringent requirements."
```

**Example - Acceptable (limitation noted)**:
```
Selected: false
State: Ohio

System Note: "State-specific Ohio regulations not analyzed. Ohio has EPA-authorized 
RCRA program. State requirements MUST be separately reviewed and may supersede 
federal requirements."
```

#### Field 5.3: Conservative Interpretation Preference

**Field Name**: `conservative_interpretation`

**Type**: Single selection (required)

**Allowed Values**:
- `strictly_conservative` - Always apply most stringent interpretation
- `balanced_conservative` - Conservative interpretation with note of alternatives (DEFAULT)
- `analyze_alternatives` - Present multiple interpretations without preference

**Validation Rules**:
- MUST select exactly one value

**Default**: `balanced_conservative` (system default)

**System Behavior**:
- `strictly_conservative`: System applies strictest interpretation, notes alternatives minimally
- `balanced_conservative`: System applies conservative interpretation but explicitly discusses alternatives
- `analyze_alternatives`: System presents multiple interpretations, flags that professional judgment required

**Recommendation**: System recommends `balanced_conservative` as default to maintain defensibility while providing transparency.

**Example - Acceptable**:
```
Selected: balanced_conservative

System Note: "When regulatory ambiguity exists, system will apply conservative 
interpretation but will explicitly note alternative interpretations and rationale 
for conservative choice."
```

#### Field 5.4: Output Format Preference

**Field Name**: `output_format`

**Type**: Multiple selection (required)

**Allowed Values**:
- `markdown` - Markdown format (always available)
- `pdf` - PDF format (if available)
- `docx` - Microsoft Word format (if available)
- `html` - HTML format (if available)

**Validation Rules**:
- MUST select at least one value
- `markdown` is always available
- Other formats subject to system capabilities

**Default**: `markdown`

**Example - Acceptable**:
```
Selected: markdown, pdf
```

#### Field 5.5: Acknowledgment of Limitations and Disclaimers

**Field Name**: `acknowledgment`

**Type**: Boolean (required)

**Value**: Must be `true` to proceed

**Display Text**:
```
I acknowledge and understand:

✓ This system provides informational research only, NOT legal advice

✓ All outputs must be reviewed by qualified environmental compliance 
  professionals and legal counsel before any reliance or action

✓ The system may not identify all applicable requirements or regulatory nuances

✓ Professional judgment is required for facility-specific compliance determinations

✓ I am responsible for:
  - Verifying applicability to my specific situation
  - Confirming currency of all cited regulations
  - Consulting qualified professionals before taking action
  - Complying with all applicable regulatory requirements

✓ The system developers, operators, and contributors disclaim all liability 
  for any damages, losses, or consequences resulting from use of or reliance 
  on system outputs

I acknowledge these limitations and disclaimers and agree to use system outputs 
for informational purposes only, subject to professional review.
```

**Validation Rules**:
- MUST check box to acknowledge
- CANNOT proceed without acknowledgment

**Error Behavior**: If not acknowledged, display: "You must acknowledge the system limitations and disclaimers to proceed. If you do not agree, please do not use this system."

**Example - Acceptable**:
```
☑ Acknowledged
```

**Example - Unacceptable**:
```
☐ Not acknowledged
```
❌ **Error**: Cannot proceed without acknowledgment

#### Field 5.6: Contact for Follow-up Questions (Optional)

**Field Name**: `contact_email`

**Type**: Email address (optional)

**Validation Rules**:
- Optional field
- If provided, must be valid email format
- Used only for system communication about this query
- Not stored beyond query lifecycle

**Purpose**: Allows system to request clarification if needed during analysis

**Privacy Note**: "Email used only for this query. Not shared or stored beyond query processing."

**Default**: Empty

**Example - Acceptable**:
```
user@example.com
```

---

### Step 5: Validation Summary

**Before Proceeding to Analysis**:

✓ **MUST Validate**:
- [ ] Analysis depth selected (Field 5.1)
- [ ] If state specified, state research preference selected (Field 5.2)
- [ ] Conservative interpretation preference selected (Field 5.3)
- [ ] Output format(s) selected (Field 5.4)
- [ ] Limitations and disclaimers acknowledged (Field 5.5)

✓ **MUST Confirm**:
- User has acknowledged system limitations
- User understands output is not legal advice
- User commits to professional review before reliance

❌ **MUST NOT Proceed If**:
- Acknowledgment not provided
- Required preferences not selected

---

## Cross-Step Validation and Consistency Checks

### Consistency Validation

**System MUST Check**:

1. **Domain-Question Alignment**:
   - Question in Step 2 must relate to domain selected in Step 1
   - If mismatch detected, request clarification

2. **Jurisdiction-Facts Alignment**:
   - If state specified in Step 1, state-specific facts in Step 3 should be consistent
   - Flag if facility appears to be in different state than Step 1 selection

3. **Question-Activity Alignment**:
   - Activity description (Step 3) should relate to question (Step 2)
   - Flag if disconnect detected

4. **Temporal Consistency**:
   - Deadline dates should be consistent with regulatory date scope
   - Flag if analyzing historical regulations for future deadline

### Assumption Documentation

**System MUST Document**:

For every `unknown`, `not specified`, or default value used:
- **What** was unknown or assumed
- **Why** it matters (impact on analysis)
- **How** it's handled (conservative assumption, limitation, etc.)

**Example Assumption Log**:
```
ASSUMPTIONS BASED ON USER INPUT:

1. Federal jurisdiction: ASSUMED (user selected default "true")
   Impact: Analysis includes federal regulations
   Limitation: State authorization status not verified

2. Facility size: UNKNOWN (user selected "unknown")
   Impact: Analysis addresses Large Quantity Generator requirements (most stringent)
   Limitation: If facility is actually SQG or VSQG, different requirements apply

3. State jurisdiction: SPECIFIED (California)
   Impact: Analysis includes note about California authorized program
   Limitation: Detailed California regulatory analysis subject to Field 5.2 selection

4. Substances: GENERIC (general "ignitable waste")
   Impact: Analysis addresses general ignitable waste requirements
   Limitation: Substance-specific requirements may apply based on actual composition
```

---

## Failure Modes and Required System Responses

### Failure Mode 1: Insufficient Information to Answer Question

**Trigger**: Required information missing despite wizard completion

**System MUST**:
1. Identify specific information gaps
2. Explain why information is necessary
3. Request specific additional information
4. MUST NOT guess or assume missing critical information
5. MUST NOT proceed with analysis if gaps prevent defensible answer

**System Response Template**:
```
INSUFFICIENT INFORMATION TO COMPLETE ANALYSIS

The following information is required to address your question but was not provided:

1. [Specific missing information]
   Why needed: [Explanation]
   How to provide: [Guidance]

2. [Additional missing information]
   Why needed: [Explanation]
   How to provide: [Guidance]

OPTIONS:

A. Provide additional information requested above
B. Revise question to be answerable with available information
C. Proceed with limited analysis (limitations will be extensively documented)

Recommendation: [System recommendation based on gap severity]
```

**Example**:
```
INSUFFICIENT INFORMATION TO COMPLETE ANALYSIS

Your question asks about NPDES permit requirements for stormwater discharge, 
but the following information is required:

1. Facility location (state and whether in MS4 area)
   Why needed: Permit requirements vary significantly by state and MS4 status
   How to provide: Specify facility city and state

2. Discharge point characteristics
   Why needed: Different requirements apply based on discharge location and volume
   How to provide: Describe where stormwater discharges and approximate volume

OPTIONS:

A. Provide facility location and discharge characteristics
B. Revise question to ask about general federal NPDES requirements only
C. Proceed with generic federal analysis (state-specific requirements not addressed)

Recommendation: Option A (provide information) for facility-specific analysis
```

### Failure Mode 2: Question Outside System Scope

**Trigger**: Question is outside environmental compliance or requests prohibited advice

**System MUST**:
1. Clearly state question is outside scope
2. Explain why (not environmental compliance, requests legal advice, etc.)
3. Suggest alternative resources if appropriate
4. MUST NOT attempt to answer outside-scope questions

**System Response Template**:
```
QUESTION OUTSIDE SYSTEM SCOPE

Your question appears to be outside the scope of environmental regulatory 
compliance analysis.

Issue: [Specific reason question is out of scope]

This system is designed for:
✓ [List in-scope topics]

This system cannot address:
✗ [List out-of-scope topics including user's question category]

Suggested Resources:
- [Relevant alternative resources]

Please revise your question to focus on environmental regulatory compliance, 
or consult appropriate professionals for your specific needs.
```

**Example**:
```
QUESTION OUTSIDE SYSTEM SCOPE

Your question asks about litigation strategy for defending against an EPA 
enforcement action.

Issue: This is a request for legal advice and litigation strategy, which is 
outside this system's scope.

This system is designed for:
✓ Regulatory interpretation and compliance requirements
✓ Citation research and regulatory text analysis
✓ Informational compliance research

This system cannot address:
✗ Legal advice or litigation strategy
✗ Attorney-client privileged communications
✗ Enforcement defense or settlement negotiations

Suggested Resources:
- Environmental law attorney licensed in your jurisdiction
- Specialized environmental enforcement defense counsel

URGENT: If you are facing enforcement action, consult qualified legal counsel 
immediately. Do not rely on this system for enforcement response.
```

### Failure Mode 3: Ambiguous or Conflicting Input

**Trigger**: User inputs are contradictory or ambiguous

**System MUST**:
1. Identify specific ambiguity or conflict
2. Request clarification
3. Provide options for resolution
4. MUST NOT guess at user intent

**System Response Template**:
```
CLARIFICATION NEEDED - AMBIGUOUS INPUT

Your inputs contain an ambiguity or apparent conflict:

Conflict: [Describe specific conflict]

Your inputs:
- [First input]
- [Second conflicting input]

Please clarify:
[Specific clarification questions]

Options:
A. [First interpretation]
B. [Second interpretation]
C. [Other option]

Please select the option that matches your situation, or revise inputs to resolve ambiguity.
```

**Example**:
```
CLARIFICATION NEEDED - AMBIGUOUS INPUT

Your inputs contain an apparent conflict between regulatory domain and question:

Conflict: Regulatory domain selected as "RCRA Hazardous Waste" but question 
asks about Clean Water Act NPDES permit requirements.

Your inputs:
- Step 1: Regulatory domain = RCRA Hazardous Waste
- Step 2: Question about NPDES permit requirements (Clean Water Act)

Please clarify:
Is your question about:
A. RCRA requirements (correct domain selection, revise question)
B. Clean Water Act requirements (revise domain to Clean Water Act)
C. Both RCRA and Clean Water Act (submit as two separate questions)

Please select the option that matches your needs.
```

### Failure Mode 4: Information Quality Issues

**Trigger**: User provided information appears inaccurate, implausible, or problematic

**System MUST**:
1. Flag potential issue
2. Request verification or clarification
3. Explain why issue is problematic
4. Allow user to confirm or revise

**System Response Template**:
```
INFORMATION VERIFICATION REQUESTED

The following input may contain an issue:

Issue: [Describe potential problem]

Your input: [Specific input]

Potential problem: [Why this may be incorrect or implausible]

Please verify:
- Is this information correct as entered?
- If incorrect, please revise
- If correct, confirm and system will proceed with this information (and note any limitations)

Options:
A. Revise input
B. Confirm input is correct
```

**Example**:
```
INFORMATION VERIFICATION REQUESTED

The following input may contain an issue:

Issue: Unusually high hazardous waste generation rate

Your input: "Facility generates 50,000 kg hazardous waste per month"

Potential problem: This generation rate is extremely high for most industrial 
facilities and would trigger multiple additional regulatory requirements beyond 
standard LQG requirements.

Please verify:
- Is the quantity correct (50,000 kg/month = ~50 metric tons/month)?
- If this is actually 5,000 kg or 500 kg, please revise
- If 50,000 kg is correct, confirm and system will analyze as very large generator

This verification is important because generation rate determines applicable 
regulations and compliance obligations.

Options:
A. Revise quantity
B. Confirm 50,000 kg/month is correct
```

---

## Input Validation Rules Summary

### Field-Level Validation

**MUST Validate**:
- Data type (text, date, number, selection)
- Required vs. optional
- Length constraints
- Format constraints (dates, citations, etc.)
- Allowed values (for selections)

**Error Handling**:
- Clear, specific error messages
- Explanation of why input is invalid
- Guidance on how to correct
- No generic "invalid input" messages

### Cross-Field Validation

**MUST Validate**:
- Consistency between related fields
- Conditional requirements met
- No contradictory inputs
- Alignment between domain, question, and facts

### Semantic Validation

**SHOULD Validate** (request clarification, not hard error):
- Question quality and specificity
- Factual plausibility
- Scope appropriateness
- Alignment with system capabilities

---

## Default Values and Conservative Assumptions

### When Defaults Are Applied

**Defaults MUST Be**:
- Explicitly documented in output
- Conservative (erring on side of compliance)
- Accompanied by explanation and limitation note

### Conservative Default Principles

**When Information Is Unknown**:
1. **Jurisdiction**: Assume federal regulations apply AND state may be more stringent
2. **Facility Size**: Assume larger classification (more stringent requirements)
3. **Substance Hazard**: Assume more stringent classification
4. **Timeline**: Assume shorter compliance periods
5. **Exemptions**: Do NOT assume exemptions apply without verification

**Example Default Application**:
```
INPUT: Generator status unknown

CONSERVATIVE DEFAULT: Analysis addresses Large Quantity Generator requirements 
(most stringent)

DOCUMENTED AS:
"Generator status not specified. This analysis addresses Large Quantity Generator 
(LQG) requirements, which are most stringent. If facility is Small Quantity 
Generator (SQG) or Very Small Quantity Generator (VSQG), different requirements 
apply. Generator status determination required."
```

---

## Examples: Complete Wizard Flows

### Example 1: Acceptable Complete Flow (RCRA Question)

**Step 1: Regulatory Domain and Jurisdiction**
```
1.1 Regulatory Domain: rcra_hazardous_waste
1.2 Other Specification: [N/A]
1.3 Federal Jurisdiction: true
1.4 State/Territory: California
1.5 Local Jurisdiction: unknown
```

**Step 2: Specific Regulatory Question**
```
2.1 Question: "Under RCRA regulations, what are the maximum accumulation time 
    limits for a large quantity generator storing ignitable hazardous waste in 
    containers at an industrial facility in California?"
2.2 Question Category: timeline_deadline
2.3 Known Citations: "40 C.F.R. § 262.34"
```

**Step 3: Factual Context**
```
3.1 Facility Type: industrial_manufacturing
3.2 Facility Size: large_quantity_generator
3.3 Substances: "Ignitable hazardous waste (primarily spent solvents - toluene, 
    xylene, acetone)"
3.4 Activity: "On-site accumulation of spent solvents in 55-gallon steel drums. 
    Approximately 2,500 kg hazardous waste generated per month. Waste stored in 
    designated outdoor area on concrete pad with secondary containment. Quarterly 
    pickup for off-site disposal."
3.5 Additional Facts: "Facility has no prior violations. Located in industrial 
    zone, not in floodplain."
```

**Step 4: Temporal Scope**
```
4.1 Date Scope: current_regulations
4.2 Specific Date: [N/A]
4.3 Future Date: [N/A]
4.4 Urgency: routine_planning
4.5 Deadline Date: [N/A]
```

**Step 5: Preferences and Confirmation**
```
5.1 Analysis Depth: focused
5.2 State Research: true
5.3 Conservative Interpretation: balanced_conservative
5.4 Output Format: markdown, pdf
5.5 Acknowledgment: true ☑
5.6 Contact: user@example.com
```

✅ **VALIDATION**: All required fields complete, consistent, specific, appropriate scope

**SYSTEM PROCEEDS TO ANALYSIS**

---

### Example 2: Unacceptable Flow (Triggers Failure Mode)

**Step 1: Regulatory Domain and Jurisdiction**
```
1.1 Regulatory Domain: other
1.2 Other Specification: [EMPTY]  ← ERROR
1.3 Federal Jurisdiction: unknown
1.4 State/Territory: unknown
1.5 Local Jurisdiction: unknown
```

❌ **VALIDATION ERROR**: Field 1.2 required when "other" selected

**Step 2: Specific Regulatory Question**
```
2.1 Question: "What do I need to do?"  ← ERROR: Too vague
2.2 Question Category: [NOT SELECTED]  ← ERROR: Required field
2.3 Known Citations: [Empty]
```

❌ **VALIDATION ERRORS**: 
- Question too vague/general
- Question category not selected

**SYSTEM RESPONSE**:
```
INCOMPLETE OR INVALID INPUT - CANNOT PROCEED

The following issues must be addressed:

1. Regulatory Domain (Step 1, Field 1.2)
   Error: "Other" regulatory domain selected but not specified
   Required Action: Specify the regulatory program, statute, or environmental regulation

2. Compliance Question (Step 2, Field 2.1)
   Error: Question is too vague to address
   Current: "What do I need to do?"
   Required Action: Provide specific compliance question including:
   - Specific regulatory issue or requirement
   - Relevant facts or situation
   - Specific compliance determination needed

3. Question Category (Step 2, Field 2.2)
   Error: Required field not selected
   Required Action: Select the category that best describes your question

Please revise inputs and resubmit.
```

❌ **SYSTEM DOES NOT PROCEED TO ANALYSIS**

---

### Example 3: Acceptable Flow with Clarification Requests

**Step 1: Regulatory Domain and Jurisdiction**
```
1.1 Regulatory Domain: clean_water_act
1.2 Other Specification: [N/A]
1.3 Federal Jurisdiction: true
1.4 State/Territory: unknown  ← LIMITATION
1.5 Local Jurisdiction: unknown
```

⚠️ **SYSTEM NOTE**: "State jurisdiction not specified. Analysis will address federal 
requirements only. State regulations MUST be separately reviewed."

**Step 2: Specific Regulatory Question**
```
2.1 Question: "What are NPDES permit requirements for stormwater discharge from 
    construction site?"  ← NEEDS MORE INFO
2.2 Question Category: permit_requirement
2.3 Known Citations: [Empty]
```

⚠️ **CLARIFICATION REQUEST**:
```
Your question about NPDES stormwater permits for construction sites requires 
additional information:

Required Information:
1. Construction site size (acres disturbed)
   Why: Different requirements for sites >1 acre vs. <1 acre
   
2. State location
   Why: State-specific requirements may apply
   
3. Discharge location (municipal separate storm sewer, surface water, etc.)
   Why: Affects permit requirements

OPTIONS:
A. Provide additional information in Step 3
B. Proceed with generic analysis (limitations will be extensively documented)
C. Revise question to be more specific

Recommendation: Provide information in Step 3 for most accurate analysis
```

**User Selects**: Option A - Will provide in Step 3

**Step 3: Factual Context**
```
3.1 Facility Type: construction_site
3.2 Facility Size: construction_over_1_acre  ← CLARIFIES
3.3 Substances: "Sediment, construction site runoff"
3.4 Activity: "5-acre residential development site. Grading and excavation. 
    Stormwater discharges to municipal storm sewer system which discharges to 
    river. Construction duration approximately 18 months."  ← PROVIDES NEEDED INFO
3.5 Additional Facts: "Construction expected to begin April 2024."
```

✅ **SYSTEM NOTE**: "Additional information provided in Step 3 addresses clarification 
requests. Analysis can proceed."

**Steps 4-5: Complete as normal**

✅ **VALIDATION**: Clarification requests satisfied, analysis can proceed with documented limitations for unknown state

**SYSTEM PROCEEDS TO ANALYSIS WITH DOCUMENTED LIMITATIONS**

---

## System Behavior Requirements Summary

### MUST DO

✓ **Request Clarification When**:
- Information is ambiguous
- Question is too broad
- Inputs are contradictory
- Required context is missing

✓ **Document Assumptions When**:
- Defaults are applied
- Information is unknown
- Conservative interpretation chosen
- Jurisdictional scope limited

✓ **Fail Safely When**:
- Critical information missing
- Question is unanswerable
- Outside system scope
- Cannot verify critical facts

✓ **Validate At Each Step**:
- Field-level validation
- Cross-field consistency
- Semantic appropriateness
- Completeness before proceeding

### MUST NOT DO

✗ **Never Guess**:
- User intent
- Missing facts
- Jurisdictional details
- Facility characteristics

✗ **Never Assume**:
- Exemptions apply
- Less stringent requirements apply
- Information is not important
- Generic analysis is sufficient

✗ **Never Proceed When**:
- Critical information missing and cannot be conservatively assumed
- Question is outside scope
- Acknowledgment not provided
- Validation errors exist

✗ **Never Minimize**:
- Limitations
- Information gaps
- Uncertainty
- Need for professional review

---

## Input Wizard Quality Metrics

### Success Metrics

**Successful Input Session**:
- All required fields completed
- Validation passes
- No critical information gaps (or conservatively addressed)
- Acknowledgment obtained
- User understands scope and limitations

**Metrics to Track**:
- Completion rate (% reaching Step 5)
- Clarification request rate
- Average completion time
- User revision rate
- Validation error rate by field

### Quality Indicators

**High Quality Input**:
- Specific, focused question
- Relevant jurisdiction identified
- Sufficient factual context
- Appropriate scope
- Realistic expectations

**Low Quality Input** (requires clarification):
- Vague or broad question
- Missing jurisdiction
- Insufficient facts
- Multi-part question
- Unrealistic expectations

---

## Document Control

**Version**: 1.0
**Effective Date**: 2024-01-15
**Last Updated**: 2024-01-15
**Status**: MANDATORY - NON-NEGOTIABLE
**Approval**: Required for all system inputs

**Revision History**:
- v1.0 (2024-01-15): Initial contract definition

---

*This input contract is mandatory for all EnviroEcon Compliance Copilot system inputs. Deviations require formal approval and documentation.*
