# Windows Server RDS Learning Path 52 — Design and Pilot MFA for RD Gateway

**Level:** Advanced · **Module:** 52/70

## Goal
Design, implement and validate a limited pilot of a supported multifactor-authentication integration for RD Gateway without implying that a provider-neutral command can configure every MFA product.

> MFA integration is provider-specific. This module is complete only when a supported product has been selected, its documented RD Gateway integration has been configured in the lab, and the acceptance tests below have passed. Architecture documentation alone is not a completed MFA deployment.

## Prerequisites
- Working RD Gateway deployment
- Selected MFA product with documented support for the Windows Server and RD Gateway versions in the lab
- Required licensing and test tenant
- Dedicated pilot group
- Protected recovery account excluded only through an approved emergency-access policy
- Provider installation, rollback and support documentation

## Implementation workflow
1. Draw the full authentication flow from the external client to RD Gateway, the Windows authentication service, Active Directory and the MFA provider.
2. Record the exact supported provider architecture, components, ports, certificates, service accounts and availability dependencies.
3. Back up the current RD Gateway and authentication-service configuration.
4. Install and configure the provider integration by following the selected vendor's current supported procedure.
5. Scope the integration to a dedicated pilot group.
6. Confirm timeout values account for the second-factor interaction without creating excessive authentication windows.
7. Define fail-open or fail-closed behavior explicitly and obtain approval.
8. Document a protected recovery path and test it separately.
9. Test an approved request, a rejected request, an unavailable-provider scenario and rollback.
10. Compare timestamps across RD Gateway, Windows authentication and provider logs.

## Baseline validation

```powershell
Get-Service -Name IAS,TSGateway -ErrorAction Stop |
    Format-Table Name,Status,StartType -AutoSize

Get-WinEvent `
    -LogName 'Microsoft-Windows-TerminalServices-Gateway/Operational' `
    -MaxEvents 100 -ErrorAction SilentlyContinue |
    Select-Object TimeCreated,Id,LevelDisplayName,Message

Get-WinEvent -LogName Security -MaxEvents 100 -ErrorAction SilentlyContinue |
    Select-Object TimeCreated,Id,LevelDisplayName,Message
```

Use the provider's supported health and log interface to confirm that its authentication component is reachable and processing only pilot-group requests.

## Acceptance tests

| Test | Expected result |
|---|---|
| Approved pilot user with valid primary credentials and valid second factor | Allowed |
| Approved pilot user rejects or times out the second factor | Denied |
| User outside the pilot group | Existing approved behavior, as documented by the pilot design |
| Invalid primary credentials | Denied before access is granted |
| MFA provider unavailable | Matches the approved fail-open/fail-closed design |
| Protected recovery account | Works only through the documented emergency path |
| Rollback | Restores the previously approved Gateway authentication flow |

## Evidence
Store the architecture diagram, selected product/version, vendor support reference, pilot scope, sanitized configuration summary, all acceptance-test results, timestamp correlation, recovery procedure, rollback test and final pass/fail status under `evidence/`. Remove secrets and personal information.

## Troubleshooting
- Authentication delay: correlate the exact request timestamp across the client, Gateway, Windows authentication service and provider.
- Pilot users bypass MFA: verify group scope and policy order in both Windows and provider configuration.
- All users are blocked: validate provider reachability, certificates, time synchronization, service health and failover policy.
- Rollback fails: restore the backed-up authentication configuration and verify the original test path before continuing.

## Security
Use a narrow pilot, least-privilege service identities, protected recovery accounts and explicit availability behavior. Never treat an untested fail-open state as acceptable by default.

## Rollback
Remove the provider integration using its supported procedure, restore the backed-up Gateway/authentication configuration, restart only the documented affected services, and repeat the original pre-pilot connection test.

## Next
`Windows-Server-RDS-Learning-Path-53-Troubleshoot-RDS-Domain-Join-Failures`
