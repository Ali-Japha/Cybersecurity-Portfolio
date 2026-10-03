# User & SSH Key Security Checklist
**Client:** SmileCare Dental Clinic - IT Operations Team
**Prepared By:** Muhammad Ali Hussain, Cybersecurity Consultant
**Date:** October 2026

## 1. Executive Summary
This checklist establishes the mandatory identity and access management (IAM) baseline for SmileCare's Linux infrastructure. The objective is to eliminate password-based brute-force vulnerabilities, enforce non-repudiation (accountability), and secure remote administrative access via asymmetric cryptography.

## 2. User Account Management
To ensure a secure audit trail, all shared administrative accounts must be dismantled. 

- [ ] **Enforce Individual Accounts:** Every IT staff member must have a uniquely named, unprivileged account (e.g., `jsmith`). 
- [ ] **Disable Direct Root Login:** Administrators must log into their individual accounts and use `sudo` to execute administrative commands, ensuring all high-level actions are logged to a specific human.
- [ ] **Audit Orphaned Accounts:** Identify and lock accounts of former employees using `passwd -l [username]`.
- [ ] **Verify Sudoers:** Review the `/etc/sudoers` file. Only authorized IT personnel should belong to the `sudo` or `wheel` group.

## 3. SSH Cryptographic Standards
Administrators must generate secure cryptographic key pairs for remote access.

- [ ] **Use Modern Algorithms:** Generate keys using ED25519 (preferred for speed and security) or RSA-4096. 
  *Command: `ssh-keygen -t ed25519 -C "admin@smilecare.local"`*
- [ ] **Mandate Private Key Passphrases:** All private keys must be encrypted with a local passphrase. If an administrator's laptop is stolen, the private key cannot be used without this passphrase.
- [ ] **Audit Authorized Keys:** Regularly review the `~/.ssh/authorized_keys` file for every user. Delete any public keys belonging to unknown devices or departed staff.

## 4. SSH Daemon Hardening (`sshd_config`)
The server's SSH service must be hardened to reject insecure connection attempts. Modify `/etc/ssh/sshd_config` with the following parameters:

- [ ] **Disable Password Authentication:** 
  `PasswordAuthentication no`
  *(Forces all users to authenticate via SSH keys; eliminates brute-force risk).*
- [ ] **Disable Root Login over SSH:** 
  `PermitRootLogin no`
  *(Prevents attackers from targeting the universal `root` account directly).*
- [ ] **Disable Empty Passwords:** 
  `PermitEmptyPasswords no`
- [ ] **Restart the Service:** 
  Apply changes by running `systemctl restart sshd`. **(Warning: Ensure your public key is successfully tested before disabling passwords, or you will lock yourself out).**
