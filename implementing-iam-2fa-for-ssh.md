# Implementing IAM: 2FA for SSH with Google Authenticator / Authy

> Beginner‑friendly, step‑by‑step guide to add **two‑factor authentication (2FA)** to **OpenSSH** on Linux using **PAM Google Authenticator**. Demo inspired by James Stanger’s walkthrough.

---

## What you’ll build
- SSH logins that require **something you have** (a time‑based code from Authy/Google Authenticator) **plus** your existing method (public key or password).
- A safe rollback plan so you don’t lock yourself out.

## Prerequisites
- A Linux server (e.g., **Ubuntu 18.04+**) with **OpenSSH server**.
- A second terminal or console access in case you need to roll back.
- A TOTP app: **Authy** or **Google Authenticator** on your phone.
- Accurate server time (TOTP is time‑based). Ensure NTP is enabled:
  ```bash
  timedatectl
  sudo timedatectl set-ntp true
  ```

---

# Implementing IAM: 2FA for SSH

## Step 0 — Safety first
Keep one **root** or **sudo** session open locally/over console while you test 2FA from a **separate** terminal. If anything goes wrong, you can revert.

## Step 1 — Install the Google Authenticator PAM module
```bash
sudo apt update
sudo apt install -y libpam-google-authenticator
```
This installs:
- The PAM module: `pam_google_authenticator.so`
- The setup tool: `google-authenticator`

## Step 2 — Enable keyboard‑interactive auth in SSH
Open the SSH server config:
```bash
sudo nano /etc/ssh/sshd_config
```

Add or adjust the following:

```conf
# Required to let PAM prompt for the 2FA code
KbdInteractiveAuthentication yes
UsePAM yes

# Strongly recommended: require BOTH public key and OTP (no passwords)
# (Available in OpenSSH 6.2+)
AuthenticationMethods publickey,keyboard-interactive
PubkeyAuthentication yes
PasswordAuthentication no
```

> **Older OpenSSH versions (e.g., some 18.04 configs):** you may see `ChallengeResponseAuthentication`. If so, set:
> ```conf
> ChallengeResponseAuthentication yes
> UsePAM yes
> ```
> (On newer OpenSSH, `KbdInteractiveAuthentication` replaces it.)

Save the file, but **do not restart** SSH yet.

## Step 3 — Tell PAM to use Google Authenticator for SSH
Edit the SSH PAM stack:
```bash
sudo nano /etc/pam.d/sshd
```

Add this **near the top** (after any `#%PAM-1.0` header), **before** `@include common-auth` lines if present:

```conf
# Require a TOTP code for SSH logins
auth  required  pam_google_authenticator.so
```

**Options you can append:**
- `nullok` — allow users without 2FA enrolled to log in (useful for gradual rollout)
- `secret=/path` — custom secret file location (default is `~/.google_authenticator`)
- `echo_verification_code` — shows the entered code (handy for labs, not for prod)

Example for gradual rollout:
```conf
auth required pam_google_authenticator.so nullok
```

Save the file.

## Step 4 — Enroll your user for 2FA
Run on the **account that will log in via SSH** (not as root unless you really SSH as root):
```bash
google-authenticator
```
Answer the prompts:
- **Time‑based tokens?** → `y` (recommended)
- Scan the **QR code** with Authy/Google Authenticator.
- Store the **emergency scratch codes** somewhere safe.
- Accept rate‑limiting and file‑rewrite prompts (`y` recommended).

This creates `~/.google_authenticator` with your secret.

## Step 5 — Restart SSH and test
Restart SSH **after** enrolling at least one user:
```bash
sudo systemctl restart ssh
```

From a **different terminal** (or another machine), try logging in:
```bash
ssh youruser@your.server.ip
```
- If you set `AuthenticationMethods publickey,keyboard-interactive`, you’ll first authenticate with your **SSH key**, then be prompted for a **Verification code** (your TOTP).
- If you left password auth enabled, you’ll enter your password and then the TOTP.

You should see a successful login after entering the 6‑digit code.

## Step 6 — Make it robust (recommended hardening)
In `/etc/ssh/sshd_config`:
```conf
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
AuthenticationMethods publickey,keyboard-interactive
LoginGraceTime 30
MaxAuthTries 3
```
Then:
```bash
sudo systemctl reload ssh
```

## Step 7 — (Optional) Rollout rules and exceptions
You can target 2FA per user, group, or network with **Match** blocks:
```conf
# Example: enforce 2FA for admins only
Match Group admins
  AuthenticationMethods publickey,keyboard-interactive
  PasswordAuthentication no

# Example: skip 2FA on a trusted internal subnet (be cautious)
Match Address 10.0.0.0/24
  AuthenticationMethods publickey
```
> Every `Match` block overrides settings **below** it until the next `Match` or file end.

## Step 8 — Troubleshooting
- Check logs:
  ```bash
  sudo journalctl -u ssh -e
  # or on Debian/Ubuntu:
  sudo tail -f /var/log/auth.log
  ```
- Time drift breaks TOTP. Confirm:
  ```bash
  timedatectl status
  ```
- File permissions:
  ```bash
  ls -l ~/.google_authenticator
  chmod 600 ~/.google_authenticator
  ```
- PAM order matters. Ensure the `pam_google_authenticator.so` line is **before** any final `pam_deny.so` in `/etc/pam.d/sshd`.

## Step 9 — Safe rollback / disable 2FA
If you get locked out or need to disable:
1. Console in (or use your still‑open safety session).
2. Revert `/etc/pam.d/sshd` (comment or remove the `pam_google_authenticator` line).
3. In `/etc/ssh/sshd_config`, allow a fallback (temporarily):
   ```conf
   PasswordAuthentication yes
   #AuthenticationMethods publickey,keyboard-interactive
   ```
4. Restart SSH:
   ```bash
   sudo systemctl restart ssh
   ```

---

## FAQ
**Do I need Internet for the codes?**  
No. TOTP codes are generated locally by your phone and verified by your server.

**Can I use Authy instead of Google Authenticator?**  
Yes—any TOTP app (Authy, 1Password, Microsoft/Google Authenticator) works.

**Can I keep passwords?**  
You can, but stronger is **SSH key + TOTP**. If you must keep passwords, require `keyboard-interactive` so TOTP is still enforced.

**Where are the secrets stored?**  
Per-user in `~/.google_authenticator`. Protect this file with `chmod 600` and secure home directories.

---

## Summary
You enabled SSH 2FA via **PAM Google Authenticator**, requiring an app‑generated code in addition to your existing method. This significantly reduces the risk of compromised credentials and aligns with IAM best practices.

---

### Appendix: Minimal working configs

**/etc/pam.d/sshd**
```conf
#%PAM-1.0
auth  required  pam_google_authenticator.so
@include common-auth
@include common-account
@include common-session
@include common-password
```

**/etc/ssh/sshd_config (modern OpenSSH)**
```conf
UsePAM yes
KbdInteractiveAuthentication yes
PubkeyAuthentication yes
PasswordAuthentication no
AuthenticationMethods publickey,keyboard-interactive
```

**/etc/ssh/sshd_config (older style)** 
```conf
UsePAM yes
ChallengeResponseAuthentication yes
PubkeyAuthentication yes
PasswordAuthentication no
# AuthenticationMethods line may not exist on very old versions
```
