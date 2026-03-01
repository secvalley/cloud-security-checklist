# Contributing to Cloud Security Checklist

Thanks for your interest in contributing. This project improves when security practitioners share their knowledge.

## How to Contribute

### Adding New Controls

1. Fork the repository
2. Find the appropriate checklist file (e.g., `azure/README.md`)
3. Add your control under the correct category
4. Submit a pull request

### Control Format

Every control must follow this format:

```markdown
- [ ] **Control Name** `Severity: Critical|High|Medium|Low`
  Description of what to check, why it matters, and how to remediate.
  > Reference: [CIS Benchmark X.Y.Z](link) | [Vendor Docs](link)
```

**Required fields:**
- Checkbox (`- [ ]`)
- Bold control name
- Severity level (Critical, High, Medium, or Low)
- Description (1-2 sentences: what to check and why)
- At least one reference link

### Severity Guidelines

| Level | Use When |
|-------|----------|
| `Critical` | Direct path to data breach, account takeover, or full environment compromise |
| `High` | Significant security weakness that could be exploited with moderate effort |
| `Medium` | Defense-in-depth control that reduces attack surface |
| `Low` | Best practice that improves security posture incrementally |

### Suggesting Changes to Existing Controls

If a control is outdated, inaccurate, or could be improved:

1. Open an issue describing the problem
2. Reference the specific control and checklist
3. Suggest the correction with supporting documentation

### Adding a New Platform or Category

For entirely new checklists or categories:

1. Open an issue first to discuss scope
2. Follow the existing directory structure (`platform/README.md`)
3. Include at least 15 controls across multiple categories
4. Base controls on CIS Benchmarks or equivalent industry standards

## Pull Request Process

1. Ensure your controls follow the format above
2. Verify all links are valid
3. Keep descriptions concise and actionable
4. One PR per logical change (don't mix Azure and AWS changes)
5. Provide context in the PR description for why the control matters

## What We're Looking For

- Controls based on CIS Benchmarks, NIST, or vendor security best practices
- Real-world hardening steps (not theoretical)
- Clear, jargon-free descriptions that a mid-level engineer can act on
- Valid reference links to official documentation

## What We Won't Merge

- Marketing content or vendor-specific product recommendations
- Controls without references or justification
- Duplicate controls already covered in the checklist
- Overly broad guidance (e.g., "secure your network") without specific steps

## Code of Conduct

Be respectful, constructive, and focused on improving cloud security for everyone.

## Questions?

Open an issue or reach out to the maintainers.
