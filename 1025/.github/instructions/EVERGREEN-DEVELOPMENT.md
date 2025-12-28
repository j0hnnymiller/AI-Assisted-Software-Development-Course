# Evergreen Software Development

## Introduction

Evergreen software development is an approach that ensures your software remains current, secure, and maintainable over time. Rather than allowing technical debt to accumulate, evergreen practices emphasize continuous updates, modern standards, and proactive maintenance.

Use this instruction when planning software maintenance strategies, establishing development workflows, implementing dependency management processes, or guiding teams toward sustainable long-term software practices. Following these guidelines will help prevent technical debt accumulation and keep your codebase modern and secure.

## Audience and Scope

- **Audience**: Development teams, technical leads, software architects, and DevOps engineers responsible for maintaining and evolving software systems
- **Scope**:
  - Continuous update strategies and cadences
  - Modern development standards and practices
  - Technical debt management approaches
  - Dependency and framework maintenance
  - Team practices for sustainable development
  - Technology-specific guidelines (.NET, Frontend, Database)
  - Metrics and monitoring for software health
- **Out of scope**:
  - Specific product roadmap planning
  - Business feature prioritization decisions
  - Project management methodologies
  - Individual tool-specific configurations (covered in separate documentation)
  - Emergency hotfix procedures

## Prerequisites

- Understanding of software development lifecycle
- Familiarity with dependency management for your tech stack
- Access to CI/CD pipeline configuration
- Authority to allocate time for maintenance activities
- Team buy-in for continuous improvement practices
- Basic knowledge of version control (Git)
- Understanding of semantic versioning

## Output Requirements

This instruction produces organizational practices and processes rather than specific file outputs. However, teams implementing these practices should create and maintain:

- **Dependency update schedule**: Documented review cadence (weekly/monthly/quarterly) in team wiki or project management tool
- **Migration roadmap**: Markdown file or issue tracker items documenting planned upgrades
- **Health metrics dashboard**: Regular reports on dependency age, vulnerabilities, test coverage
- **Architecture Decision Records (ADRs)**: Markdown files documenting major technical decisions in `.github/adr/` or `docs/adr/`
- **Team practice documentation**: Updates to team handbook or README files
- **Format**: Markdown for documentation, YAML/JSON for configuration files
- **Self-contained**: All documentation should be clear and maintainable within the repository

- **Self-contained**: All documentation should be clear and maintainable within the repository

## Steps / Procedure

### Step 1: Assess Current State

1. Inventory all dependencies and their current versions
2. Identify outdated or End-of-Life (EOL) components
3. Document current framework and runtime versions
4. Measure baseline metrics: test coverage, code quality, build success rate
5. Review existing technical debt backlog

**Expected outcome**: Complete inventory of your software stack's current state

### Step 2: Establish Automation Infrastructure

1. Set up automated dependency scanning tools:
   - Configure Dependabot, Renovate, or similar for dependency updates
   - Enable Snyk or WhiteSource for security vulnerability scanning
2. Configure CI/CD pipeline with quality gates:
   - Add automated testing on every commit/PR
   - Implement code quality checks (linting, formatting)
   - Add security scanning stages
   - Set up test coverage reporting
3. Enable automated alerts for security vulnerabilities

**Expected outcome**: Automated tooling detecting issues and proposing updates

### Step 3: Define Update Cadence and Responsibilities

1. Establish review schedule:
   - **Weekly**: Check for security vulnerabilities
   - **Monthly**: Review and update patch versions
   - **Quarterly**: Evaluate minor version upgrades
   - **Annually**: Plan major version migrations
2. Assign rotating responsibility for dependency reviews
3. Schedule regular team time for maintenance work
4. Document the cadence in team documentation

**Expected outcome**: Clear schedule and ownership for maintenance activities

### Step 4: Implement Core Principles

#### Continuous Updates

- Regularly update dependencies, frameworks, and runtime versions
- Use automated tools for dependency updates
- Treat security patches as critical priorities
- Plan framework upgrades as part of regular development cycles

#### Modern Standards

- Adopt new stable language features as they mature
- Follow evolving industry best practices and design patterns
- Maintain consistent code style using automated formatters
- Use current API design standards (REST, GraphQL, gRPC as appropriate)

#### Technical Debt Management

- Schedule dedicated time for refactoring in each sprint/iteration
- Document and track technical debt items in your issue tracker
- Apply the Boy Scout Rule: leave code better than you found it
- Plan removal of deprecated features proactively

#### Testing and Quality

- Maintain high test coverage (aim for 80%+)
- Run automated tests on every commit/PR
- Enforce quality standards before merging code
- Continuously monitor and optimize performance

### Step 5: Execute Updates Safely

1. **Test in Development**: Apply updates in dev environment first
2. **Verify Compatibility**: Run full test suite after updates
   - Unit tests
   - Integration tests
   - End-to-end tests
3. **Review Breaking Changes**: Check changelogs for breaking changes
4. **Staged Rollout**: Deploy through staging environment before production
5. **Monitor**: Watch metrics and logs after deployment
6. **Rollback Plan**: Have tested rollback procedure ready

**Expected outcome**: Updates deployed safely with minimal risk

### Step 6: Maintain Language and Framework Currency

#### For .NET Applications:

- Use latest stable .NET version (currently .NET 9)
- Follow Microsoft's support lifecycle
- Plan upgrades within 1 year of new major release
- Validate on preview versions before GA (General Availability)
- Use latest C# language version available for target framework
- Leverage nullable reference types for better null safety
- Use System.Text.Json for modern JSON handling

#### For Frontend Applications:

- Keep Node.js on current LTS version
- Update bundlers and build tools regularly
- Use modern JavaScript/TypeScript features
- Leverage framework-specific upgrade tools
- Monitor bundle size and performance metrics

#### For Database Systems:

- Apply database patches on regular schedule
- Plan database version upgrades annually
- Test all migrations in non-production environments first
- Maintain database schema versioning
- Document breaking changes in schema

### Step 7: Monitor and Measure Health

Track these key health indicators:

- **Dependency Age**: Average age of dependencies (target: < 6 months)
- **Security Vulnerabilities**: Number of known vulnerabilities (target: 0 critical/high)
- **Code Coverage**: Test coverage percentage (target: > 80%)
- **Build Success Rate**: CI/CD pipeline success rate (target: > 95%)
- **Deployment Frequency**: How often you can safely deploy (target: daily capability)

**Expected outcome**: Dashboard or regular report showing software health trends

### Step 8: Conduct Regular Reviews

- **Monthly**: Review dependency health report, address critical issues
- **Quarterly**: Assess technical debt backlog, plan reduction efforts
- **Bi-Annually**: Evaluate framework/platform versions, plan upgrades
- **Annually**: Comprehensive technology stack review, long-term planning

**Expected outcome**: Regular touchpoints ensuring continuous improvement

### Step 9: Choose Migration Strategy When Needed

For incremental updates (preferred):

1. Break changes into small, manageable pieces
2. Deploy changes frequently with low risk per change
3. Continue feature work alongside updates
4. Validate each increment before proceeding

For major migrations:

1. Assess if "Big Bang" is necessary (usually only for breaking platform changes)
2. For legacy systems, consider Strangler Fig Pattern:
   - Identify boundaries for new functionality
   - Implement new features with modern stack
   - Gradually migrate existing features
   - Eventually retire old system
3. Plan extensive testing and validation
4. Prepare detailed rollback procedures

**Expected outcome**: Successful upgrades with minimal disruption

## Examples / Samples

### Example: Weekly Security Review

```bash
# Run security scan
dotnet list package --vulnerable

# Output (ideal state):
The given project has no vulnerable packages
```

### Example: CI/CD Pipeline Configuration

```yaml
# Example pipeline stages for evergreen practices
stages:
  - dependency-check # Scan for vulnerable dependencies
  - build # Build with latest tools
  - test # Run comprehensive test suite
  - quality-analysis # Code quality and coverage checks
  - security-scan # Security vulnerability scanning
  - deploy # Automated deployment
```

### Example: Migration Comparison

**Incremental Approach (Preferred):**

```markdown
Sprint 1: Update all patch versions
Sprint 2: Upgrade minor versions with no breaking changes
Sprint 3: Update test frameworks
Sprint 4: Migrate to new API patterns incrementally
```

**Big Bang Approach (When Necessary):**

```markdown
Month 1-2: Preparation and testing in isolated environment
Month 3: Full migration over planned maintenance window
Month 4: Stabilization and bug fixes
```

## Templates / Snippets

### Dependency Update Schedule Template

```markdown
## Dependency Update Schedule

### Weekly (Every Monday)

- [ ] Run security vulnerability scan
- [ ] Review and address critical/high severity issues
- [ ] Check automated dependency PR status

### Monthly (First Friday)

- [ ] Review patch version updates
- [ ] Test and merge dependency PRs
- [ ] Update development dependencies
- [ ] Review dependency age metrics

### Quarterly (Start of Q1, Q2, Q3, Q4)

- [ ] Evaluate minor version upgrades
- [ ] Review framework/platform roadmaps
- [ ] Plan breaking change migrations
- [ ] Update migration roadmap

### Annual (January)

- [ ] Comprehensive technology stack review
- [ ] Plan major version migrations
- [ ] Review and update team practices
- [ ] Set goals for dependency health metrics
```

### Health Metrics Report Template

```markdown
## Software Health Report - [Date]

### Dependency Health

- Average dependency age: [X] months (Target: < 6)
- Outdated dependencies: [X] (Target: < 10%)
- Security vulnerabilities: [X] Critical, [X] High (Target: 0)

### Code Quality

- Test coverage: [X]% (Target: > 80%)
- Build success rate: [X]% (Target: > 95%)
- Code quality score: [X]/10 (Target: > 8)

### Platform Versions

- .NET version: [X] (Latest: [Y])
- Node.js version: [X] (Latest LTS: [Y])
- Database version: [X] (Latest: [Y])

### Action Items

1. [Priority action based on metrics]
2. [Next priority action]
```

### ADR Template for Version Upgrades

```markdown
# ADR [Number]: Upgrade to [Technology] [Version]

## Status

[Proposed | Accepted | Deprecated | Superseded]

## Context

- Current version: [X]
- Target version: [Y]
- Key drivers: [Security | Features | Performance | Support lifecycle]

## Decision

We will upgrade to [Technology] [Version] because:

1. [Reason 1]
2. [Reason 2]

## Consequences

### Positive

- [Benefit 1]
- [Benefit 2]

### Negative

- [Challenge 1]
- [Challenge 2]

### Migration Plan

1. [Step 1]
2. [Step 2]

## References

- [Link to technology roadmap]
- [Link to migration guide]
```

## Validation / Quality Checklist

Use this checklist to verify your evergreen practices are properly implemented:

- [ ] Automated dependency scanning is configured and running
- [ ] Security vulnerability alerts are enabled and monitored
- [ ] CI/CD pipeline includes quality gates for code coverage and linting
- [ ] Update cadence is documented and followed
- [ ] Team members have rotating responsibility for updates
- [ ] Health metrics are tracked and reviewed regularly
- [ ] Dependencies are less than 6 months old on average
- [ ] No critical or high severity vulnerabilities present
- [ ] Test coverage exceeds 80%
- [ ] Build success rate exceeds 95%
- [ ] Framework/runtime versions are within support windows
- [ ] Technical debt items are tracked and prioritized
- [ ] Migration roadmap exists for upcoming major changes
- [ ] Team has time allocated for maintenance work
- [ ] Documentation is current and reflects actual practices

## File Placement and Naming

Documentation created as part of evergreen practices should follow these conventions:

- **Architecture Decision Records**: `.github/adr/NNNN-title.md` or `docs/adr/NNNN-title.md`
- **Migration roadmaps**: `docs/migrations/[technology]-[version]-migration.md`
- **Health reports**: `docs/health-reports/YYYY-MM.md` or in team wiki
- **Update schedules**: Include in team `README.md` or `docs/processes/dependency-updates.md`

## Versioning and Ownership

- **Owner**: Development Team Lead / Technical Architect
- **Created**: 2025-10-22
- **Last updated**: 2025-10-22
- **Change policy**: Submit pull request for review. Significant changes to core principles require team discussion and consensus.
- **Review cycle**: Review this instruction quarterly to ensure it reflects current best practices

## Security and Privacy

When implementing evergreen practices:

- **Never commit**: API keys, passwords, tokens, connection strings, or credentials
- **Dependency updates**: Review changelogs for security-related changes before applying
- **Vulnerability scanning**: Configure alerts to notify appropriate team members
- **Private dependencies**: Use private package feeds with authentication for proprietary code
- **Secrets management**: Use secure secret stores (Azure Key Vault, AWS Secrets Manager, etc.)
- **Security patches**: Treat as highest priority and apply immediately after testing
- **Compliance**: Ensure updates maintain compliance with organizational security policies

If security vulnerabilities are discovered:

1. Assess severity and impact
2. Apply patches in development environment
3. Run security scans to verify fix
4. Deploy through staging with monitoring
5. Document incident and resolution

## References / Glossary

### Key Terms

- **Evergreen**: Software that is continuously updated to remain current
- **Technical Debt**: Implied cost of additional rework caused by choosing quick solutions over better approaches
- **EOL (End of Life)**: Date after which software is no longer supported
- **LTS (Long-Term Support)**: Software version receiving extended support
- **Semantic Versioning**: Version numbering scheme (MAJOR.MINOR.PATCH)
- **Boy Scout Rule**: Leave code better than you found it
- **Strangler Fig Pattern**: Gradual migration pattern that wraps old system with new functionality
- **ADR (Architecture Decision Record)**: Document capturing important architectural decision

### Tools and Resources

**Dependency Management:**

- [Dependabot](https://github.com/dependabot) - GitHub's automated dependency updates
- [Renovate](https://www.mend.io/renovate/) - Flexible dependency update automation
- [Snyk](https://snyk.io/) - Security vulnerability scanning
- [WhiteSource](https://www.whitesourcesoftware.com/) - Open source security and license compliance

**Code Quality:**

- [SonarQube](https://www.sonarqube.org/) - Code quality and security analysis
- [CodeClimate](https://codeclimate.com/) - Automated code review
- [ReSharper](https://www.jetbrains.com/resharper/) - .NET code quality tools
- [ESLint](https://eslint.org/) / [Prettier](https://prettier.io/) - JavaScript/TypeScript quality

**Monitoring:**

- [Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) - Azure application monitoring
- [Datadog](https://www.datadoghq.com/) - Infrastructure and application monitoring
- [New Relic](https://newrelic.com/) - Application performance monitoring
- [Sentry](https://sentry.io/) - Error tracking and monitoring

**Best Practices:**

- [.NET Support Lifecycle](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core)
- [Node.js Release Schedule](https://nodejs.org/en/about/releases/)
- [Semantic Versioning](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)
- [Architecture Decision Records](https://adr.github.io/)

### Related Documentation

- `.github/instructions/instruction-standards.instruction.md` - Standards for creating instruction files
- Team handbook section on development practices
- CI/CD pipeline documentation
- Security policy documentation

---

_Note: This document itself follows evergreen principles - update it as practices, tools, and industry standards evolve. The best time to update was yesterday. The second best time is now._
