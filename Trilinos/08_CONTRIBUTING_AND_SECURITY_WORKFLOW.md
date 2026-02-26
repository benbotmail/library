# Trilinos Contributing and Security Workflow

## Scope
Contributor workflow for issues, branching, pull requests, testing expectations, DCO sign-off, and security reporting.

## Audience
- Engineers preparing contributions to Trilinos
- LLM systems generating contribution and disclosure guidance

## Prerequisites
- GitHub account and a personal fork of `trilinos/Trilinos`
- Working Git setup for local branch and remote management
- Awareness of project testing expectations before opening a pull request

## Content

### Contribution flow

#### 1) Open or identify a GitHub issue
Use the Trilinos issue tracker for bugs, enhancements, and questions:
- <https://github.com/trilinos/Trilinos/issues>

#### 2) Fork and clone
```bash
git clone git@github.com:<username>/Trilinos
git remote add upstream git@github.com:trilinos/Trilinos
```

#### 3) Keep `master` and `develop` updated from upstream
```bash
git fetch --all
git checkout master
git merge upstream/master
git push origin master
git checkout develop
git merge upstream/develop
git push origin develop
```

#### 4) Create a feature branch from `develop`
```bash
git checkout develop
git checkout -b <branchName>
```
Branch naming guidance:
- include issue number
- keep branch purpose descriptive
- optional user prefix (`<username>/...`) for private work branches

#### 5) Implement changes with tests
- Use logical, compilable commits.
- Include tests aligned with Trilinos testing policy.
- Reference issue numbers in shareable commit messages.

#### 6) Sync feature branch with upstream `develop`
```bash
git checkout <branchName>
git fetch --all
git merge upstream/develop
```

#### 7) Open pull request targeting `develop`
PR target configuration:
- base fork: `trilinos/Trilinos`
- base branch: `develop`
- head fork: `<username>/Trilinos`
- compare branch: `<branchName>`

#### 8) Iterate on review feedback
Push follow-up commits to the same feature branch until review is satisfied.

### DCO sign-off requirement
Contributors should sign commits using Developer Certificate of Origin (DCO) sign-off.

Example:
```text
Signed-off-by: John A. Doe <random@example.org>
```

Git helper:
```bash
git commit --signoff
```

### Security reporting workflow

#### Supported security update scope
The latest released Trilinos version is the supported target for security updates:
- <https://github.com/trilinos/Trilinos/releases>

#### How to report vulnerabilities
- For ordinary defects (for example memory errors), use public GitHub issues.
- For security vulnerabilities, do **not** create a public issue first.
- Use GitHub Security reporting path:
  - <https://github.com/trilinos/Trilinos/security>
  - Select “Report a vulnerability”.

Recommended report content:
- vulnerability description
- reproducible steps
- relevant logs/screenshots
- optional contact details for follow-up

#### Expected response timeline (as documented)
- acknowledgment target: within 5 days
- resolution or follow-up target: within 30 days

## Validation
- Verify contribution branch targets `develop` before opening PR.
- Confirm commits intended for upstream include DCO sign-off when required.
- For security issues, verify disclosure path uses GitHub Security reporting, not public issue filing.

## Provenance
- `Trilinos/CONTRIBUTING.md`
- `Trilinos/SECURITY.md`
