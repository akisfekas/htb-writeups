<!--
  HOW TO USE THIS TEMPLATE
  1. Copy to machines/<BoxName>/README.md
  2. Put screenshots in machines/<BoxName>/images/ and link them as ./images/<file>.png
  3. Replace every <PLACEHOLDER>, delete sections that don't apply
  4. NEVER paste real flags, cleartext passwords or full hashes — redact them like [... root flag ...]
  5. Only publish write-ups for RETIRED machines (HTB ToS)
-->

# <BoxName> — Hack The Box Writeup

![<BoxName> info card](./images/info-card.png)

| | |
|---|---|
| **Platform** | Hack The Box |
| **Machine** | <BoxName> |
| **OS** | <Linux / Windows> (<distro / version if known>) |
| **Difficulty** | <Very Easy / Easy / Medium / Hard / Insane> |
| **IP** | `10.10.11.<X>` |
| **Author** | Akis |
| **Date** | <YYYY-MM-DD> |
| **Tags** | `<web>` `<sqli>` `<privesc>` `<...>` |

---

## Summary

<One or two sentences describing the box and the overall attack path.>

1. **<Stage 1 — e.g. Enumeration finding>** — <short description>.
2. **<Stage 2 — e.g. Initial foothold / CVE-XXXX-XXXXX>** — <short description>.
3. **<Stage 3 — e.g. Lateral movement to `user`>** — <short description>.
4. **<Stage 4 — e.g. Privilege escalation to `root`>** — <short description>.

---

## Enumeration

### Nmap

```bash
nmap -sC -sV -oA nmap/<boxname> 10.10.11.<X>
```

Open services:

| Port | Service | Version / Notes |
|------|---------|-----------------|
| 22/tcp | SSH | <OpenSSH x.x> |
| 80/tcp | HTTP | <nginx / Apache x.x — redirects to `http://<box>.htb`> |
| <port>/tcp | <service> | <notes> |

Full port sweep (to catch anything non-standard):

```bash
nmap -p- --min-rate 5000 -oA nmap/<boxname>-allports 10.10.11.<X>
```

Add the vhost to `/etc/hosts`:

```bash
echo "10.10.11.<X> <box>.htb" | sudo tee -a /etc/hosts
```

### Web — `<port>`

<What the site is, tech stack, versions found.>

```bash
whatweb http://<box>.htb
```

![Landing page](./images/landing-page.png)

#### Directory / file brute force

```bash
feroxbuster -u http://<box>.htb -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

#### Vhost / subdomain fuzzing

```bash
ffuf -u http://<box>.htb -H "Host: FUZZ.<box>.htb" \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs <size>
```

**Findings:**

- <finding 1>
- <finding 2>

### <Other service — e.g. SMB / FTP / LDAP>

```bash
<command>
```

<Notes.>

---

## Foothold — <Vulnerability name> (<CVE-XXXX-XXXXX>)

### The vulnerability

<Explain WHAT is vulnerable and WHY — root cause in your own words. This is the part that shows understanding, not just copy-paste.>

### Exploitation

```bash
<exploit command / request>
```

<Explain the important parts of the payload.>

![Exploit working](./images/foothold.png)

### Shell

```bash
# listener
nc -lvnp <PORT>
```

```bash
<trigger payload>
```

Upgrade the shell:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
# Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

Landed as `<www-data / service user>`.

---

## Lateral Movement — `<service user>` → `<user>`

### Enumeration as `<service user>`

```bash
id
sudo -l
ls -la /home
find / -perm -4000 -type f 2>/dev/null
```

<What you found — config files, DB credentials, reused passwords, etc.>

```bash
<command that revealed creds>
```

### Pivot

```bash
ssh <user>@<box>.htb
```

**user.txt:**

```
[... user flag ...]
```

---

## Privilege Escalation — `<user>` → `root`

### Discovery

```bash
sudo -l
ps aux
cat /etc/crontab
ls -la /opt /srv
```

<Optional: linpeas / pspy output highlights.>

<What stood out and why it's exploitable.>

### Exploitation

```bash
<privesc commands>
```

<Explain the technique.>

![Root shell](./images/root.png)

**root.txt:**

```
[... root flag ...]
```

---

## Attack Chain

```
<Recon> ──► <Foothold vuln> ──► <service user> ──► <cred / pivot> ──► <user> ──► <privesc vector> ──► root
```

---

## Remediation

- **<Vuln 1>:** <fix — patch version, config change, input validation, etc.>
- **<Vuln 2>:** <fix>
- **<Credential handling>:** <fix — permissions, no password reuse, secrets management>
- **<Privesc vector>:** <fix — least privilege, remove SUID, restrict sudo rules>

---

## CVE / Technique Reference

| Stage | CVE / Technique | Component | MITRE ATT&CK |
|-------|-----------------|-----------|--------------|
| Foothold | <CVE-XXXX-XXXXX> | <software + vulnerable versions> | <T1190> |
| Lateral | <technique> | <component> | <T1552> |
| Privesc | <CVE / technique> | <component> | <T1548> |

---

## Lessons Learned

- <What new technique / tool did I learn?>
- <Where did I get stuck, and what unblocked me?>
- <What would I check earlier next time?>

---

## References

- [<CVE advisory / NVD entry>](<url>)
- [<Exploit / PoC source>](<url>)
- [<Blog / docs that helped>](<url>)
- [HackTricks — <relevant page>](<url>)

---

[← Back to index](../../README.md)
