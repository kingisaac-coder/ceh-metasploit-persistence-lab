# Windows 10 Reverse Shell & Persistence Lab (Metasploit)

## Overview
A hands-on lab demonstrating the full offensive security lifecycle against a Windows 10 target in an isolated VirtualBox environment: payload generation, reverse shell establishment, post-exploitation enumeration, privilege escalation attempts, persistence technique deployment, and cleanup.

**Environment:**
- Attacker: Parrot Security OS (VirtualBox)
- Target: Windows 10 (VirtualBox, host-only/NAT network)
- Framework: Metasploit Framework v6.5.6

> All activity was performed in a fully isolated, non-production lab network for educational purposes (CEH coursework). No real-world systems were targeted.

## Methodology

### 1. Payload Generation & Delivery
Generated a `windows/meterpreter/reverse_tcp` payload with `msfvenom`, served it via a local HTTP server, and downloaded it onto the Windows 10 target.

### 2. Reverse Shell Handler
Configured `exploit/multi/handler` in msfconsole with matching LHOST/LPORT to catch the callback once the payload executed on the target.

### 3. Post-Exploitation
Explored the established Meterpreter session: `sysinfo`, `getuid`, `screenshot`, `ipconfig`, and process enumeration to confirm access and gather target context.

### 4. Privilege Escalation Attempts
- Ran `getsystem` — automated techniques (named pipe impersonation, token duplication, EfsPotato) all failed, indicating the target's token privileges weren't trivially escalatable.
- Ran `post/multi/recon/local_exploit_suggester` against the session to check the target against Metasploit's catalog of known local privesc modules. Most flagged "No — admin/SYSTEM privileges required," confirming no trivial privesc path was available at the current access level.

### 5. Persistence
Demonstrated `exploit/windows/persistence/registry` to install a registry Run-key-based persistence mechanism, surviving reboot/logoff in theory. Verified install via Metasploit output and manually via Windows Registry Editor.

**Finding:** the persistence artifact (Run key) did not survive a VM restart in testing — the payload storage key remained, but the Run key auto-removal suggests either a cleanup process, patch behavior, or incomplete install. Documented as an observed limitation.

### 6. Cleanup
Manually removed leftover registry artifacts (`HKCU\Software\jH7KdWjp`) via Registry Editor to restore the target to a clean state after testing.

## Key Takeaways
- Default `getsystem` techniques are not guaranteed on a modern, non-hardened Windows 10 build — manual/targeted exploitation research is often still required.
- Automated recon tools (`local_exploit_suggester`) are a useful first pass but don't replace manual enumeration.
- Persistence techniques should always be paired with documented cleanup steps, even in lab environments.

## Screenshots
See `/screenshots` for step-by-step evidence of each phase.

## Disclaimer
This lab was conducted strictly within a private, isolated virtual lab environment as part of authorized CEH coursework. Techniques described here should only be used in environments you own or are explicitly authorized to test.
