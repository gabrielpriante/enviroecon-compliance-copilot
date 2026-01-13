# Citation Standards

## Purpose

This document establishes the citation standards and verification requirements for EnviroEcon Compliance Copilot. Proper citation is fundamental to the system's defensibility and trustworthiness.

## Core Citation Principles

1. **Every regulatory statement requires a citation**
2. **Citations must be verifiable against authoritative sources**
3. **Citations must follow standard legal citation formats**
4. **Citations must include version/date information when relevant**

## Citation Format Standards

### Federal Regulations (Code of Federal Regulations)

**Format**: `Title C.F.R. § Section.Subsection (Year)`

**Examples**:
- ✅ `40 C.F.R. § 261.3 (2024)` - Resource Conservation and Recovery Act hazardous waste definition
- ✅ `40 C.F.R. § 122.2 (2024)` - Clean Water Act permit definitions
- ✅ `33 C.F.R. § 328.3(a)(1) (2023)` - Waters of the United States definition

**Requirements**:
- Include title number (e.g., 40 for EPA regulations)
- Use "C.F.R." abbreviation
- Use "§" symbol or "Section"
- Include subsection specificity where relevant
- Include year of regulation version

**Verification Source**: [https://www.ecfr.gov/](https://www.ecfr.gov/)

### United States Code

**Format**: `Title U.S.C. § Section (Year)`

**Examples**:
- ✅ `42 U.S.C. § 6901 et seq.` - Resource Conservation and Recovery Act
- ✅ `33 U.S.C. § 1251 et seq.` - Clean Water Act
- ✅ `42 U.S.C. § 7401 et seq.` - Clean Air Act

**Requirements**:
- Include title number
- Use "U.S.C." abbreviation
- Use "§" symbol or "Section"
- Use "et seq." for statutory ranges

**Verification Source**: [https://uscode.house.gov/](https://uscode.house.gov/)

### State Regulations

**Format**: `[State Abbreviation] [Regulatory Code] § [Section] (Year)`

**Examples**:
- ✅ `Cal. Code Regs. tit. 22, § 66261.3 (2024)` - California hazardous waste
- ✅ `6 NYCRR § 375-1.2 (2024)` - New York environmental remediation
- ✅ `N.J.A.C. 7:26E-1.6 (2024)` - New Jersey site remediation

**Requirements**:
- State-specific citation format
- Verify against state regulatory database
- Include year of regulation version
- Note if state incorporates federal regulations by reference

**Verification Sources**: Varies by state
- California: [https://oal.ca.gov/](https://oal.ca.gov/)
- New York: [https://dos.ny.gov/](https://dos.ny.gov/)
- Check state environmental agency websites

### EPA Guidance Documents

**Format**: `EPA, [Document Title], [Document Number] (Month Year)`

**Examples**:
- ✅ `EPA, RCRA Orientation Manual, EPA530-F-11-003 (Jan. 2014)`
- ✅ `EPA, Interim Guidance on Mechanical Dredging, EPA-905-B94-003 (Dec. 1994)`

**Requirements**:
- Include "EPA" as author
- Full document title
- Document/publication number if available
- Month and year of publication
- **Note in analysis**: Guidance is not binding regulation

**Verification Source**: [https://www.epa.gov/](https://www.epa.gov/)

### Court Cases (When Interpreting Regulations)

**Format**: `Case Name, Citation, Court (Year)`

**Examples**:
- ✅ `Chevron U.S.A., Inc. v. Natural Resources Defense Council, Inc., 467 U.S. 837 (1984)`
- ✅ `Rapanos v. United States, 547 U.S. 715 (2006)`

**Requirements**:
- Italicize case name (in formatted outputs)
- Include reporter citation
- Include court identification
- Include year of decision
- **Note in analysis**: Case law interpretation may vary by jurisdiction

### Federal Register Notices

**Format**: `[Title], Federal Register Volume Page (Date)`

**Examples**:
- ✅ `Definition of "Waters of the United States," 88 Fed. Reg. 3004 (Jan. 18, 2023)`
- ✅ `Hazardous Waste Pharmaceuticals, 84 Fed. Reg. 5816 (Feb. 22, 2019)`

**Requirements**:
- Include Federal Register volume
- Include page number
- Include full date
- Distinguish between proposed rules and final rules
- **Note if proposed**: Proposed rules are not enforceable

**Verification Source**: [https://www.federalregister.gov/](https://www.federalregister.gov/)

## Citation Verification Requirements

### Automated Verification

All citations must pass automated verification checks:

**Format Validation**:
- Proper structure (title, section, year)
- Valid symbols (§, C.F.R., U.S.C.)
- No malformed citations

**Source Verification**:
- Citation exists in authoritative database
- Link to source document is active
- Source document contains cited text/section

**Currency Verification**:
- Regulation is current (not superseded)
- Date/version matches cited year
- Amendment history noted if relevant

**Content Verification**:
- Quoted text matches source exactly
- Paraphrased content accurately reflects source
- No misrepresentation of regulatory requirement

### Manual Verification (Spot-Checks)

Periodic manual verification includes:
- Random sampling of citations
- Full source document review
- Context verification (does citation support claim?)
- Interpretive accuracy assessment

## Citation in Output Format

### In-Text Citation

**Regulatory Statement with Citation**:
```markdown
Under the Resource Conservation and Recovery Act, solid waste is defined as 
"any garbage, refuse, sludge from a waste treatment plant, water supply 
treatment plant, or air pollution control facility and other discarded 
material." 40 C.F.R. § 261.2 (2024).
```

**Multiple Citations for Single Point**:
```markdown
Federal wetland jurisdiction extends to waters that significantly affect 
interstate commerce. 33 C.F.R. § 328.3(a)(1) (2023); see also Rapanos v. 
United States, 547 U.S. 715, 729-730 (2006).
```

**Citation with Explanatory Parenthetical**:
```markdown
The definition of hazardous waste excludes certain recycled materials. 
40 C.F.R. § 261.2(e) (2024) (excluding materials recycled by being used 
in certain specified manners).
```

### Reference Section

All outputs should include a "References" or "Citations" section:

```markdown
## References

### Federal Regulations
- 40 C.F.R. § 261.2 (2024) - Definition of solid waste
  https://www.ecfr.gov/current/title-40/chapter-I/subchapter-I/part-261/section-261.2

- 40 C.F.R. § 261.3 (2024) - Definition of hazardous waste
  https://www.ecfr.gov/current/title-40/chapter-I/subchapter-I/part-261/section-261.3

### Guidance Documents
- EPA, RCRA Orientation Manual, EPA530-F-11-003 (Jan. 2014)
  https://www.epa.gov/sites/default/files/2015-07/documents/rom.pdf

### State Regulations
- Cal. Code Regs. tit. 22, § 66261.3 (2024) - California hazardous waste
  https://govt.westlaw.com/calregs/Document/[specific-document-id]
```

**Requirements for Reference Section**:
- Organized by source type
- Full citation for each source
- Active hyperlink to authoritative source
- Brief description of relevance

## Special Citation Scenarios

### Incorporated Federal Standards

When state regulations incorporate federal regulations:

```markdown
California incorporates the federal hazardous waste regulations by reference. 
Cal. Code Regs. tit. 22, § 66261.3 (2024) (incorporating 40 C.F.R. § 261.3).
```

### Superseded Regulations

When citing historical regulations:

```markdown
Under the previous definition of "waters of the United States," [description]. 
40 C.F.R. § 230.3(s) (2015) (superseded 2023). The current definition is found 
at 33 C.F.R. § 328.3 (2023).
```

### Proposed But Not Final Regulations

```markdown
EPA has proposed amendments to the hazardous waste generator regulations. 
Hazardous Waste Generator Improvements Rule, 80 Fed. Reg. 57,918 (proposed 
Sept. 25, 2015) (to be codified at 40 C.F.R. pts. 260-262, 265, 268, 273).

**NOTE**: This is a proposed rule and is not currently enforceable. Current 
requirements are found at [cite current regulation].
```

### Agency Informal Guidance

```markdown
EPA has informally indicated in guidance that [position]. EPA, [Guidance Title] 
at [page] (Date).

**NOTE**: This guidance is not legally binding regulation. The authoritative 
regulatory requirement is [cite regulation].
```

## Prohibited Citation Practices

### ❌ Vague References
```markdown
❌ "Environmental regulations require..."
❌ "Under EPA rules..."
❌ "Federal law mandates..."
```

### ✅ Specific Citations
```markdown
✅ "40 C.F.R. § 261.3 requires..."
✅ "Under the Clean Water Act, 33 U.S.C. § 1311..."
✅ "Federal hazardous waste regulations at 40 C.F.R. Part 261 mandate..."
```

### ❌ Secondary Source Citations
```markdown
❌ "According to [textbook/website], the regulation states..."
❌ "Industry guidance suggests that 40 C.F.R. § 261.3 means..."
```

### ✅ Primary Source Citations
```markdown
✅ "40 C.F.R. § 261.3 states: '[exact regulatory text]'"
✅ "The regulation defines hazardous waste as... 40 C.F.R. § 261.3"
```

### ❌ Unsupported Generalizations
```markdown
❌ "Most states require..."
❌ "Regulations typically mandate..."
❌ "Standard practice is..."
```

### ✅ Specific, Cited Statements
```markdown
✅ "California requires [specific requirement]. Cal. Code Regs. tit. 22, § [X]"
✅ "The federal regulation requires [specific requirement]. 40 C.F.R. § [X]"
```

## Quality Assurance

### Pre-Output Checks

Before any output is delivered:
- [ ] Every regulatory statement has a citation
- [ ] All citations verified against authoritative sources
- [ ] All links tested and functional
- [ ] Citation format consistent throughout
- [ ] Reference section complete
- [ ] No secondary source reliance without primary verification
- [ ] Proposed vs. final rules clearly distinguished
- [ ] Guidance vs. regulation clearly distinguished

### Testing Requirements

Citation verification tests must include:
- Valid citation acceptance tests
- Invalid citation rejection tests
- Dead link detection tests
- Hallucinated citation detection tests
- Format consistency tests
- Source verification tests

### Error Handling

When citation cannot be verified:
- **Block output** - Do not proceed with unverified citations
- **Log error** - Record attempted citation and failure reason
- **Return to user** - Explain that verification failed
- **Request clarification** - If user-provided citation, ask for correction
- **Never guess** - Do not attempt to "fix" citation without verification

## Maintenance and Updates

### Regular Citation Review
- Monthly: Check for superseded regulations
- Quarterly: Verify all links remain active
- Annually: Review and update citation database
- As needed: Update for major regulatory changes

### Citation Database Updates
- Track regulatory amendments
- Update version years
- Note effective dates of changes
- Archive historical citations

## Resources

### Authoritative Sources
- **Federal Regulations**: https://www.ecfr.gov/
- **U.S. Code**: https://uscode.house.gov/
- **Federal Register**: https://www.federalregister.gov/
- **EPA**: https://www.epa.gov/
- **Regulations.gov**: https://www.regulations.gov/

### Citation Guides
- The Bluebook: A Uniform System of Citation
- ALWD Guide to Legal Citation
- Agency-specific style guides

### State Resources
- State environmental agency websites
- State regulatory code databases
- State legal citation guides

---

*Proper citation is non-negotiable. When in doubt, over-cite rather than under-cite.*
