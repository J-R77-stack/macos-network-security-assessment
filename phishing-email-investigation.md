# Phishing Email Investigation

**Course:** CompTIA Security+ SY0-701  
**Environment:** macOS Home Lab   
**Investigation Type:** Simulated phishing email analysis

## 1. Objective

Investigate a simulated phishing email using macOS command-line tools, identify social engineering indicators, examine email authentication results and preserve evidence using SHA-256 hashing.

## 2. Tools Used

- macOS Terminal
- `sed`
- `grep`
- `shasum`
- `cat`

## 3. Investigation Scenario

An employee reports an email claiming to originate from IT Support.

The message warns that their Microsoft 365 account will be disabled unless they verify their credentials within 30 minutes.

The email contains a link to a lookalike Microsoft login address.

All email addresses and domains used in this exercise were simulated using reserved `.example` domains.

## 4. Email Header Analysis

The following indicators were identified:

| Indicator | Finding |
|---|---|
| Sender | Lookalike Microsoft domain using `0` instead of `o` |
| Subject | Urgent account suspension warning |
| Reply-To | Different domain from sender |
| SPF | Fail |
| DKIM | None |
| DMARC | Fail |

These findings are consistent with a phishing attempt.

The authentication results were simulated training data rather than results independently verified by a receiving mail server.

## 5. Suspicious URL Analysis

Extracted the following URL without visiting it:

`https://micros0ft-security.example/login`

The domain uses a zero in place of the letter `o`, demonstrating typosquatting and brand impersonation.

The presence of HTTPS does not establish that a website is legitimate.

No suspicious websites were visited during the investigation.

## 6. Evidence Integrity

Generated a SHA-256 hash of the simulated email using:

```bash
shasum -a 256 suspicious-email.eml
```

**SHA-256:**

`29bfb8ffbaea65fd88120f60bb8174b4ea7873273751a6cbdf057ce9e49cb320`

A second hash calculation produced the same result.

This confirmed that the file contents had not changed between the two checks, to the assurance provided by SHA-256.

## 7. Incident Classification

**Classification:** Suspected phishing — simulated exercise

**Supporting evidence:**

- Deceptive sender domain
- Urgent account suspension threat
- Mismatched sender and reply-to domains
- Suspicious login URL
- Failed SPF and DMARC results
- No DKIM signature

The combination of indicators supports the phishing classification within the training scenario.

## 8. Recommended Response

For a real incident:

1. Preserve the original email and its headers.
2. Record relevant indicators and evidence hashes.
3. Report the message through the organisation's incident response process.
4. Investigate whether other employees received the message.
5. Apply authorised containment measures, such as blocking confirmed malicious domains or removing messages from affected mailboxes.
6. If credentials were submitted, follow the account compromise response procedure.

## 9. Security+ Concepts Demonstrated

- Phishing and social engineering
- Typosquatting
- Email header investigation
- SPF, DKIM and DMARC
- Indicators of compromise
- Incident classification
- Evidence preservation
- SHA-256 hashing
- Security awareness and incident response

## 10. Lessons Learned

1. Urgency and impersonation are common phishing techniques.
2. Lookalike domains can deceive users into trusting fraudulent websites.
3. Email authentication failures provide useful evidence but must be interpreted in context.
4. HTTPS does not guarantee website legitimacy.
5. Analysts should avoid opening suspicious links during initial investigation.
6. Hashes help verify evidence integrity.
7. Clear documentation is essential for effective incident response.

## 11. Conclusion

Successfully investigated a simulated phishing email using macOS command-line tools.

Identified multiple social engineering indicators, examined authentication results, extracted a suspicious URL safely and verified evidence integrity using SHA-256.

The exercise developed practical skills relevant to entry-level SOC analyst and cybersecurity roles.
