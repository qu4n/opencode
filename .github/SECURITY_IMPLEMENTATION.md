# GitHub Security Implementation Guide

This document describes the security checks and automated workflows implemented in this repository.

## Overview

The OpenCode repository now includes comprehensive security measures to protect against vulnerabilities, manage dependencies, and maintain code quality. These measures are implemented through GitHub's native security features and automated workflows.

## Security Features Implemented

### 1. Dependabot (`.github/dependabot.yml`)

**Purpose**: Automatically detect and update vulnerable dependencies

**What it does**:
- Monitors npm dependencies in the main workspace
- Tracks GitHub Actions versions
- Monitors Go module dependencies
- Creates automated pull requests for updates
- Groups related dependencies to reduce PR noise
- Runs weekly on Mondays at 9:00 AM

**Configuration highlights**:
- Separate grouping for production and development dependencies
- Security-focused labeling
- Customized commit message prefixes
- Limited to 10 open PRs at a time to prevent overwhelming maintainers

**How to use**:
- Dependabot will automatically create PRs when updates are available
- Review and merge the PRs after testing
- You can manually trigger checks from the Insights > Dependency graph > Dependabot tab

### 2. CodeQL Security Scanning (`.github/workflows/codeql.yml`)

**Purpose**: Automated code analysis to detect security vulnerabilities

**What it does**:
- Scans JavaScript/TypeScript, Python, and Go code
- Detects common security issues like:
  - SQL injection vulnerabilities
  - Cross-site scripting (XSS)
  - Command injection
  - Path traversal
  - Insecure cryptography
  - And many more (using `security-extended` and `security-and-quality` query suites)
- Runs on push to main branches, pull requests, and weekly schedule
- Uploads results to GitHub Security tab

**Configuration highlights**:
- Multi-language support with matrix strategy
- Extended security query suite for thorough analysis
- Weekly scheduled scans on Mondays at 2:30 AM UTC
- Results available in Security > Code scanning alerts

**How to use**:
- View findings in the Security tab > Code scanning
- Results are automatically added to pull requests
- Fix any detected issues before merging
- Can be manually triggered via workflow_dispatch

### 3. Dependency Review (`.github/workflows/dependency-review.yml`)

**Purpose**: Review dependency changes in pull requests before merging

**What it does**:
- Analyzes dependency changes in PRs
- Identifies vulnerabilities in new or updated dependencies
- Checks for license compliance issues
- Displays OpenSSF Scorecard scores for dependencies
- Posts summary comments on pull requests
- Blocks PRs with moderate or higher severity vulnerabilities

**Configuration highlights**:
- Fails on moderate or higher severity vulnerabilities
- Denies problematic licenses (GPL-2.0, GPL-3.0, AGPL-3.0)
- Allows common open-source licenses (MIT, Apache-2.0, BSD, ISC)
- Shows OpenSSF Scorecard for dependency health
- Warns if OpenSSF score is below 3

**How to use**:
- Automatically runs on all pull requests
- Review the dependency review comment on PRs
- Address any flagged vulnerabilities before merging
- Consider alternatives for dependencies with low security scores

### 4. OSSF Scorecard (`.github/workflows/scorecard.yml`)

**Purpose**: Provides a security posture score for the repository

**What it does**:
- Evaluates repository security practices
- Checks for:
  - Security policy presence
  - Dependency update tools
  - Code review practices
  - Signed commits
  - Branch protection
  - CI/CD security
  - And 10+ other security best practices
- Publishes results to OpenSSF REST API
- Uploads findings to GitHub Security tab

**Configuration highlights**:
- Runs weekly on Mondays at 4:00 AM UTC
- Runs on releases to production branch
- Results publicly available via OpenSSF API
- SARIF results uploaded to code scanning dashboard

**How to use**:
- View scorecard results in Security > Code scanning
- Check the OpenSSF Scorecard badge (can be added to README)
- Work to improve low-scoring areas
- Reference: https://github.com/ossf/scorecard

### 5. Enhanced Test Workflow (`.github/workflows/test.yml`)

**Purpose**: Add security checks to existing CI pipeline

**What it does**:
- Runs npm audit to check for vulnerable npm packages
- Runs Go vulnerability checking with govulncheck
- Continues on error to avoid blocking tests for now
- Provides early warning of dependency issues

**Configuration highlights**:
- Audit level set to moderate severity
- Runs after typecheck and test steps
- Non-blocking (continue-on-error: true)
- Can be made blocking by removing continue-on-error

**How to use**:
- Check CI logs for audit warnings
- Address vulnerabilities found in audits
- Consider making blocking in the future for stricter enforcement

## Security Policy (SECURITY.md)

**Purpose**: Provide clear guidelines for reporting security vulnerabilities

**What it includes**:
- Supported versions
- How to report vulnerabilities (security@sst.dev)
- Response timeline expectations
- Disclosure policy
- Security best practices for users
- Documentation of automated security measures

## Enabling Additional GitHub Security Features

### 1. Secret Scanning

**To enable** (if not already enabled):
1. Go to Settings > Code security and analysis
2. Enable "Secret scanning"
3. Enable "Push protection" to prevent secrets from being committed

**What it does**: Automatically detects common secret patterns (API keys, tokens, passwords) in commits

### 2. Private Vulnerability Reporting

**To enable**:
1. Go to Settings > Code security and analysis
2. Enable "Private vulnerability reporting"

**What it does**: Allows security researchers to privately report vulnerabilities through GitHub

### 3. Security Advisories

**To use**:
1. Go to Security > Advisories
2. Create a new security advisory when a vulnerability is discovered
3. Coordinate disclosure and patching

## Monitoring and Maintenance

### Regular Tasks

**Weekly**:
- Review Dependabot PRs and merge after testing
- Check CodeQL findings in Security tab
- Review any new dependency vulnerabilities

**Monthly**:
- Review OSSF Scorecard results
- Update security policy if needed
- Check for any unaddressed security warnings

**Per Pull Request**:
- Review dependency review results
- Ensure CodeQL checks pass
- Verify no new vulnerabilities are introduced

### Dashboard Links

- **Security Overview**: Repository > Security tab
- **Dependabot**: Insights > Dependency graph > Dependabot
- **Code Scanning**: Security > Code scanning alerts
- **Dependency Graph**: Insights > Dependency graph
- **Actions**: Actions tab (for workflow runs)

## Best Practices

1. **Keep dependencies updated**: Regularly merge Dependabot PRs
2. **Address CodeQL findings promptly**: Don't let security alerts accumulate
3. **Review dependency changes**: Check dependency review results on all PRs
4. **Monitor security tab**: Regularly check the Security tab for new alerts
5. **Document security processes**: Keep SECURITY.md updated
6. **Enable branch protection**: Require security checks to pass before merging
7. **Review AI-generated code**: Always review code changes made by OpenCode
8. **Use signed commits**: Enable commit signing for additional security
9. **Rotate credentials**: Regularly rotate API keys and tokens
10. **Stay informed**: Subscribe to GitHub Security Advisories

## Customization

### Adjusting Dependabot

Edit `.github/dependabot.yml` to:
- Change update schedule
- Add/remove package ecosystems
- Modify grouping strategies
- Adjust PR limits

### Adjusting CodeQL

Edit `.github/workflows/codeql.yml` to:
- Add/remove languages
- Change query suites
- Modify scan schedule
- Adjust build steps

### Adjusting Dependency Review

Edit `.github/workflows/dependency-review.yml` to:
- Change severity threshold
- Modify allowed/denied licenses
- Adjust OpenSSF score warnings

## Troubleshooting

### CodeQL fails to analyze code
- Check if the language is properly configured in the matrix
- Verify build-mode is appropriate for the language
- Review CodeQL logs in the Actions tab

### Dependabot PRs not appearing
- Check if Dependabot is enabled in repository settings
- Verify dependabot.yml syntax is correct
- Check Dependabot logs in Insights > Dependency graph

### Dependency review blocking PRs
- Review the specific vulnerabilities flagged
- Update dependencies to non-vulnerable versions
- Request a security review if needed

## Resources

- [GitHub Security Features](https://docs.github.com/en/code-security)
- [CodeQL Documentation](https://codeql.github.com/docs/)
- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [OSSF Scorecard](https://github.com/ossf/scorecard)
- [OpenSSF Best Practices](https://bestpractices.coreinfrastructure.org/)

## Questions or Issues?

For security-related questions: security@sst.dev
For general support: support@sst.dev
For bug reports: https://github.com/sst/opencode/issues
