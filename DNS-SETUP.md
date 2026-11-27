# thephonebench.com — DNS & email setup (Namecheap)

Everything below is done in the Namecheap account that holds **thephonebench.com**.
Two jobs: (1) point the domain at GitHub Pages, (2) turn on email forwarding so
**info@thephonebench.com** lands in Alfred's inbox (Instagram's verification code
for the new account will be sent to that address — this has to work before the IG
signup can finish).

Prerequisite for both: the domain must be on **Namecheap BasicDNS** (the default
nameservers — `dns1.registrar-servers.com` / `dns2.registrar-servers.com`).
Check under Domain List → Manage → Domain tab → Nameservers. If it says
"Custom DNS", switch it back to "Namecheap BasicDNS" or none of the records
below will take effect.

---

## 1. GitHub Pages — DNS records

In Namecheap: **Domain List → thephonebench.com → Manage → Advanced DNS tab.**

Delete any existing records on `@` or `www` that conflict (a default "URL Redirect"
record or a parking-page A record will fight these — remove them).

Add these five records:

| Type  | Host  | Value                   | TTL       |
|-------|-------|-------------------------|-----------|
| A     | @     | 185.199.108.153         | Automatic |
| A     | @     | 185.199.109.153         | Automatic |
| A     | @     | 185.199.110.153         | Automatic |
| A     | @     | 185.199.111.153         | Automatic |
| CNAME | www   | codesforequity.github.io | Automatic |

Notes:
- The four A records are GitHub Pages' apex IPs — all four, not just one.
- The `www` CNAME points at the GitHub *account* Pages host
  (`codesforequity.github.io`), not at thephonebench.com. GitHub routes it to
  this site because the repo's CNAME file says `thephonebench.com`.

## 2. GitHub side (after the DNS records exist)

In the GitHub repo that holds this site (Settings → Pages):

1. Source: deploy from branch `main`, folder `/ (root)` — the site is plain
   HTML, no build step.
2. Custom domain: enter `thephonebench.com` and save. (The CNAME file in the
   repo already contains it; entering it here is what binds the repo.)
3. Wait for the DNS check to pass, then tick **Enforce HTTPS**. GitHub
   provisions a Let's Encrypt certificate automatically once DNS resolves —
   this can take anywhere from a few minutes to a few hours after the records
   propagate. Until the certificate exists, the HTTPS checkbox is greyed out;
   that is normal, not an error.

Verify from any terminal once it has had time to propagate:

```
dig +short thephonebench.com        → should return the four 185.199.x.x IPs
dig +short www.thephonebench.com    → should return codesforequity.github.io
```

## 3. Email forwarding — info@thephonebench.com → alfred-trulli@muse.ai

Still in Namecheap, same domain: **Manage → Email tab.**

1. Find **Mail Settings** and set it to **Free Email Forwarding**.
   (This makes Namecheap write the MX records for its forwarding servers
   automatically. Do not pick "No Email Service" or the forwarder will never
   receive anything, and do not hand-edit the MX records it creates.)
2. In the **Email Forwarding** section, add a forwarder:
   - Address: `info` (i.e. **info@thephonebench.com**)
   - Forward to: **alfred-trulli@muse.ai**
3. Save. Forwarding usually starts working within ~30 minutes once the MX
   records propagate.

Optional but useful: also add a **catch-all** forwarder (`All` / `*` →
alfred-trulli@muse.ai) so codes sent to any other @thephonebench.com address
(support@, hello@, a typo) still arrive.

Why this matters: the Instagram account for Benched is being created with
info@thephonebench.com. Instagram emails a verification code during signup —
with this forwarder in place, the code arrives in Alfred's mailbox and the
signup can be completed. Without it, mail to the domain goes nowhere.

## Order of operations

1. Confirm BasicDNS nameservers.
2. Add the 5 DNS records (§1).
3. Set Mail Settings to Free Email Forwarding and add the info@ forwarder (§3).
4. GitHub repo: set the custom domain, wait for the check, enforce HTTPS (§2).
5. Send a test email to info@thephonebench.com from another account and
   confirm it lands in Alfred's inbox before starting the Instagram signup.
