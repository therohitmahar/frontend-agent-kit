# Security Policy

`frontend-agent-kit` is a documentation and instruction repository. It does not ship runtime code, a package, or a hosted service.

## Reporting A Vulnerability

If you find a security issue in an example or instruction, please open a GitHub issue with:

- the affected file
- the risky pattern
- the safer replacement you recommend

Do not include secrets, tokens, private keys, or private project code in public issues.

## Scope

In scope:

- examples that encourage unsafe frontend behavior
- instructions that could cause agents to leak secrets or mishandle user data
- guidance that conflicts with browser or React security basics

Out of scope:

- vulnerabilities in projects that copied and modified these instructions
- requests to audit private applications
- dependency vulnerabilities, because this repo has no dependencies
