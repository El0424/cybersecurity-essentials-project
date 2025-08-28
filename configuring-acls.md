# Configuring Access Control Lists (ACLs)

This repository provides notes and demonstrations on **Configuring Access Control Lists (ACLs)** in Cisco routers.  
Click the links below to jump to specific parts of the video.

---

## Access Control Lists
Access Control Lists (ACLs) allow granular control over **how data flows across networks** and which hosts or protocols are allowed or denied.  
- Use `permit` for allowed traffic.  
- Use `deny` for restricted traffic.  
- Order matters: specific rules first, general rules later.  
- At the end of every ACL, there’s an **implicit deny** for all other traffic.

---

## Defining Lists
Example: Disallow FTP traffic but allow everything else.

```cisco
access-list 10 deny tcp any any eq ftp
access-list 10 permit ip any any
```

⚠️ Remember: Anything not explicitly permitted is denied.

---

## Configuring ACLs
In this demo, a **Windows 10 VM** initially has full Internet access.

Steps:
1. Connect to the Cisco router (via console or Tera Term).  
2. Check the host IP:  
   ```powershell
   ipconfig
   ```
   Example: `10.0.0.2`  
3. Enter configuration mode and create an ACL:  
   ```cisco
   configure terminal
   access-list 10 deny host 10.0.0.2
   ```

---

## Types of ACLs
- **Standard ACLs**: Filter by **source IP only**.  
- **Extended ACLs**: Filter by **protocols, ports, and source/destination IPs**.

Example of a standard ACL:
```cisco
access-list 10 deny host 10.0.0.2
```

Example of an extended ACL:
```cisco
access-list 100 deny icmp host 10.0.0.2 any
access-list 100 permit ip any any
```

---

## Apply ACLs
Apply the ACL to an interface:

```cisco
interface fa0/1
ip access-group 10 in
```

To remove it:

```cisco
no ip access-group 10 in
```

---

## Deny Ping with ACLs
Using an **extended ACL** to block ICMP (ping) but still allow Internet access:

```cisco
access-list 100 deny icmp host 10.0.0.2 any
access-list 100 permit ip any any

interface fa0/1
ip access-group 100 in
```

Result:
- Host cannot ping (ICMP denied).  
- Host still has Internet access (HTTP/HTTPS etc. permitted).

---

## Defining ACLs Recap
- ACLs are **sequential permit/deny statements**.  
- **Implicit deny** exists at the end of every ACL.  
- Standard ACLs = IP only.  
- Extended ACLs = IP + protocol + port filtering.

---

## Summary
In this demonstration, we:  
- Configured ACLs on a Cisco router using CLI.  
- Denied/allowed specific hosts and protocols.  
- Applied and removed ACLs from interfaces.  

**Access Control Lists are foundational for securing and managing network traffic.**
