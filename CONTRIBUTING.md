# Contributing to EnviroEcon Compliance Copilot

Thank you for your interest in contributing to EnviroEcon Compliance Copilot! This project aims to provide a defensible, citation-first AI system for environmental compliance documentation.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Core Principles](#core-principles)
- [How to Contribute](#how-to-contribute)
- [Development Guidelines](#development-guidelines)
- [Pull Request Process](#pull-request-process)
- [Citation Standards](#citation-standards)
- [Testing Requirements](#testing-requirements)

## Code of Conduct

This project adheres to a Code of Conduct that all contributors are expected to follow. Please read [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) before contributing.

## Core Principles

All contributions must align with our core design principles:

### 1. Defensibility Over Helpfulness
- Conservative interpretation over liberal interpretation
- Explicit uncertainty over implicit assumptions
- Traceable sources over convenient summaries

### 2. No Hallucinated Regulations
- Every regulatory citation must be verifiable
- Unknown information must be marked as such
- No "plausible-sounding" but unverified content

### 3. Explicit Assumptions
- Document all interpretive choices
- State limitations clearly
- Flag ambiguities prominently

### 4. Non-Legal Advice
- Maintain clear disclaimers
- Avoid language that implies legal authority
- Design for professional review, not end-user reliance

## How to Contribute

### Reporting Issues

When reporting issues, please:
- Use the issue templates provided
- Include specific examples when possible
- Describe expected vs. actual behavior
- Note potential compliance implications

### Suggesting Features

Feature suggestions should:
- Align with core principles above
- Include use cases from actual compliance work
- Consider defensibility implications
- Address how the feature maintains citation integrity

### Improving Documentation

Documentation contributions are highly valued:
- Clarify technical concepts
- Add examples from environmental compliance practice
- Improve accessibility and readability
- Maintain professional tone

## Development Guidelines

### Code Quality Standards

1. **Citation Verification**: All code that processes regulatory content must include citation verification logic
2. **Error Handling**: Fail safely - prefer errors over incorrect output
3. **Logging**: Comprehensive logging for audit trails
4. **Testing**: See Testing Requirements below

### Code Style

- Follow PEP 8 for Python code (when implemented)
- Use type hints for all function signatures
- Write self-documenting code with clear variable names
- Comment complex regulatory logic

### Documentation Requirements

Every contribution should include:
- Inline documentation for complex logic
- Updated README if behavior changes
- Example usage for new features
- Citation sources in comments

## Pull Request Process

1. **Fork and Branch**
   - Fork the repository
   - Create a feature branch: `git checkout -b feature/your-feature-name`

2. **Make Changes**
   - Follow development guidelines
   - Write/update tests
   - Update documentation

3. **Test Thoroughly**
   - Run existing test suite
   - Add tests for new functionality
   - Test edge cases and error conditions

4. **Commit with Clear Messages**
   ```
   Short (50 chars or less) summary
   
   More detailed explanatory text, if necessary. Wrap it to 72 characters.
   Explain the problem that this commit is solving, and why this approach.
   
   Reference any relevant issues: Fixes #123
   ```

5. **Submit Pull Request**
   - Use the PR template
   - Link related issues
   - Describe testing performed
   - Request review from maintainers

6. **Address Review Feedback**
   - Respond to comments
   - Make requested changes
   - Re-request review when ready

## Citation Standards

Regulatory citations must follow these standards:

### Format Requirements
- Full regulatory citation (e.g., "40 C.F.R. § 261.3")
- Date of regulation version referenced
- URL to authoritative source when available
- Section and paragraph specificity

### Verification Requirements
- Direct verification against source document
- No reliance on secondary summaries
- Flag interpretive guidance separately from regulations
- Note when citations are from draft or proposed rules

See [docs/CITATION_STANDARDS.md](docs/CITATION_STANDARDS.md) for complete requirements.

## Testing Requirements

### Required Tests

1. **Citation Verification Tests**
   - Validate citation format
   - Test source verification logic
   - Ensure invalid citations are caught

2. **Assumption Tracking Tests**
   - Verify assumptions are logged
   - Test assumption documentation in output
   - Ensure no silent assumptions

3. **Guardrail Tests**
   - Test conservative interpretation logic
   - Verify hallucination prevention
   - Validate error handling

4. **Integration Tests**
   - End-to-end memoranda generation
   - Citation chain validation
   - Output format verification

### Test Coverage Goals

- Minimum 80% code coverage for core logic
- 100% coverage for citation verification
- Comprehensive edge case testing

## Review Process

Pull requests are reviewed for:
- ✅ Alignment with core principles
- ✅ Code quality and documentation
- ✅ Test coverage and passing tests
- ✅ Citation standard compliance
- ✅ No introduction of hallucination risk
- ✅ Proper error handling

## Getting Help

- **Questions**: Open an issue with the "question" label
- **Discussions**: Use GitHub Discussions for design conversations
- **Problems**: Describe issues in detail with examples

## License

By contributing, you agree that your contributions will be licensed under the same MIT License that covers the project.

## Recognition

Contributors will be recognized in the project documentation. Significant contributions may be highlighted in release notes.

---

Thank you for helping make environmental compliance documentation more defensible and trustworthy!
