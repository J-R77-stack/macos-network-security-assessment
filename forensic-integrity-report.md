# Digital Forensics: SHA-256 Integrity Verification

## Objective
Demonstrate how SHA-256 hashing can detect changes to digital evidence.

## Environment
- macOS Terminal
- Built-in shasum utility
- Practice file: security-evidence.txt

## Methodology
1. Created a practice evidence file.
2. Calculated its SHA-256 hash.
3. Verified the original hash matched.
4. Appended text to the practice file.
5. Recalculated and compared the hashes.

## Results
Original SHA-256:
aa174e3ebc2d721ff9dfe2aa1b9ee70a0190b07e31de15667739fc2060bbd74d

Modified SHA-256:
37010fb606eeadf07329e9db66ac6295acc019caa556dd996466ba481419b28b

Result: Hash mismatch detected after modification.

## Security Findings
- SHA-256 helps verify file integrity.
- Matching hashes strongly support unchanged data.
- A mismatch indicates a difference, not necessarily malicious tampering.
- Original forensic evidence should be preserved.

## Lessons Learned
Practised SHA-256 integrity verification on macOS.
Studied forensic imaging, write blockers,
chain of custody and order of volatility.

## Scope
Educational exercise using a disposable practice file.
