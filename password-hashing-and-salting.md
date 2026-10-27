# Day 11 — Password Hashing and Salting Investigation

**Course:** CompTIA Security+ SY0-701  
**Environment:** macOS (Intel MacBook Air)  
**Lab type:** Local password security demonstration  
**Status:** Completed  

## 1. Objective

Investigate cryptographic hashing, demonstrate the avalanche effect, and examine how random salts affect password hashes.

## 2. Tools Used

- macOS Terminal
- `shasum` (SHA-256)
- OpenSSL random number generator
- `printf` for generating dummy test data

All testing used deliberately weak, fictitious passwords. No real credentials were used.

## 3. Methodology

### Experiment 1 — Basic SHA-256 hashing

Created a test file containing `password123` followed by a newline.

Executed:

```bash
shasum -a 256 ~/security-labs/day11-password-security/test-password.txt
```

**Observed hash:**

`57db1253b68b6802b59a969f750fa32b60cb5cc8a3cb19b87dac28f541dc4e2a`

Repeated the calculation and confirmed that the result matched.

**Finding:** SHA-256 hashing is deterministic when the input bytes are identical.

### Experiment 2 — Avalanche effect

Changed the dummy password from `password123` to `password124`, retaining the newline.

**Observed hash:**

`1f1ed265659f9a5eb3abe29beec3080263e5ca008d19466da9fad4a82b4340ba`

**Finding:** A one-character change resulted in a substantially different SHA-256 digest, demonstrating the avalanche effect.

### Experiment 3 — Salted hashing

Generated a random 128-bit salt using:

```bash
openssl rand -hex 16
```

**First salt:**

`611f85323bb67d5c48e81a8e2e74bd55`

Combined the salt with `password124` and calculated SHA-256.

**First salted hash:**

`9bfd9cec57b91b073003cf7915e2d73e83479dd921883e20f9ff467c2090ab5b`

Generated a second random 128-bit salt.

**Second salt:**

`bf0abbbc324d4c3df7da1c05a6d03d28`

Repeated the hashing process with the same password.

**Second salted hash:**

`5f6b5fc8a1cd2c42ece0649c64639134cd7861be8c69b3ea6f92c367917ed0a6`

**Finding:** Identical passwords produced different hashes when combined with different salts.

## 4. Security Analysis

- SHA-256 is suitable for many integrity-checking applications but is too computationally fast for secure password storage.
- Unique random salts help defeat precomputed rainbow tables and prevent identical passwords from automatically producing identical stored hashes.
- Salts do not need to be confidential.
- Salting alone does not stop offline password guessing.
- Modern password storage should use an appropriately configured password-specific hashing or key derivation algorithm, such as Argon2id, scrypt, or bcrypt.

## 5. Limitations

The experiment demonstrated simple SHA-256 hashing with salt concatenation. It did not implement or benchmark a production password-storage system.

The initial file-based hashes included a trailing newline, while the salted hashes used input without a newline. Therefore, comparisons between those experiments demonstrate differences in hashing inputs, not the isolated effect of salting alone.

No password cracking, authentication bypass, or attack against live systems was performed.

## 6. Conclusion

The lab confirmed that cryptographic hashes are deterministic, small input changes produce significantly different digests, and unique salts produce distinct hashes for otherwise identical passwords.

The exercise reinforced CompTIA Security+ concepts covering password protection, rainbow-table resistance, hashing, salting, and secure credential storage.

**Result: Day 11 practical lab successfully completed.**
