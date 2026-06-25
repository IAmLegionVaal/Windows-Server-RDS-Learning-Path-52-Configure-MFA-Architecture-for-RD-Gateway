# Windows Server RDS Learning Path 52 — Configure MFA Architecture for RD Gateway

**Level:** Advanced · **Module:** 52/70

## Goal
Plan and test a supported multifactor authentication design for RD Gateway.

## Setup
1. Draw the connection flow from the external client to RD Gateway, the authentication service, Active Directory, and the RDS hosts.
2. Confirm that the selected authentication product supports the Windows Server and Gateway design used in the lab.
3. Apply the pilot only to a dedicated test group.
4. Document timeouts, user prompts, recovery access, and behavior when the authentication service is unavailable.
5. Test an approved request, a rejected request, and a controlled availability scenario.
6. Compare timestamps in Gateway, Windows, and provider logs.
7. Record owners, support requirements, licensing, privacy, and rollback.

## Validation
```powershell
Get-Service IAS,TSGateway
Get-WinEvent -LogName 'Microsoft-Windows-TerminalServices-Gateway/Operational' -MaxEvents 100
Get-WinEvent -LogName Security -MaxEvents 100
```

## Evidence
Store the architecture diagram, pilot scope, approved and rejected tests, availability test, log correlation, recovery procedure, and final pass/fail status under `evidence/`. Remove all secret values and personal information.

## Troubleshooting
Use the exact timestamps to determine whether a delay occurred at the client, Gateway, Windows authentication service, or external provider.

## Security
Use a narrow pilot, protected recovery accounts, and an explicit availability policy. Review every exception.

## Rollback
Remove the pilot configuration and restore the previously approved Gateway authentication method.

## Next
`Windows-Server-RDS-Learning-Path-53-Troubleshoot-RDS-Domain-Join-Failures`
