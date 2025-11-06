<div align="center">

# Assignment-3: Information and Network Security (IT407)

**Unit:** Unit 2 - Cryptography  
</div>

**Name:** Garv Goel  
**Roll Number:** 2K22/EE/108  
**Date:** November 6, 2025


---

## Question 1: Symmetric Key Cryptography - DES vs AES

**Question:**
Compare DES (Data Encryption Standard) and AES (Advanced Encryption Standard) in terms of:
1. Key size and its impact on security
2. Block size and number of rounds
3. Why was DES phased out despite being widely adopted?
4. Computational complexity and suitability for modern applications

Explain with specific technical details why AES is superior for contemporary cybersecurity needs.

**Answer:**

| Aspect | DES | AES |
|--------|-----|-----|
| **Key Size** | 56-bit (+ 8 parity bits = 64-bit) | 128, 192, or 256-bit |
| **Block Size** | 64-bit | 128-bit |
| **Number of Rounds** | 16 | 10 (128-bit), 12 (192-bit), 14 (256-bit) |
| **Algorithm Type** | Feistel Network | Substitution-Permutation Network (SPN) |

**Key Differences and Reasons for DES Obsolescence:**

1. **Key Size Vulnerability:**
   - DES's 56-bit key can be brute-forced in reasonable time using modern computing power. A 1998 brute-force attack cracked DES in 56 hours.
   - AES's 256-bit key makes brute-force attacks computationally infeasible (would require 2^256 operations, estimated at trillions of years).

2. **Block Size Limitations:**
   - DES's 64-bit block size creates vulnerabilities in electronic codebook (ECB) mode when encrypting large amounts of data.
   - AES's 128-bit block size reduces the likelihood of collisions and improves security.

3. **Cryptanalytic Attacks:**
   - DES is susceptible to linear and differential cryptanalysis despite strong S-boxes due to its reduced key size.
   - AES's design with MixColumns transformation and larger state matrix resists known attacks.

4. **Modern Adoption:**
   - AES is the NIST standard (FIPS 197) and required for U.S. government classified information.
   - AES is implemented in hardware acceleration (AES-NI) on modern processors, making it faster than software-based DES.
   - 3DES (triple DES) was used as an interim solution but is now deprecated due to performance overhead.

---

## Question 2: RSA Encryption Algorithm - Mathematical Foundations and Security

**Question:**
The RSA algorithm uses two large prime numbers p and q to generate public key (e, n) and private key (d, n).

1. Explain the mathematical process of key generation including Euler's totient function φ(n).
2. Derive why the encryption formula C ≡ M^e (mod n) and decryption formula M ≡ C^d (mod n) work correctly.
3. What is the significance of keeping d private, and how would the system break if d is exposed?
4. Explain why RSA requires padding schemes like OAEP in practice, not just raw RSA.

**Answer:**

**1. RSA Key Generation Process:**

Given two large prime numbers p and q:
- n = p × q (modulus)
- φ(n) = (p-1) × (q-1) (Euler's totient function)
- Select e such that 1 < e < φ(n) and gcd(e, φ(n)) = 1 (coprime)
- Compute d such that (e × d) ≡ 1 (mod φ(n)), using the extended Euclidean algorithm
- Public key: (e, n)
- Private key: (d, n)

**2. Mathematical Correctness:**

**Encryption:** C ≡ M^e (mod n)
**Decryption:** M ≡ C^d (mod n) = (M^e)^d ≡ M^(e×d) (mod n)

By definition of d: e × d ≡ 1 (mod φ(n))
Therefore: e × d = 1 + k × φ(n) for some integer k

By Euler's theorem: M^φ(n) ≡ 1 (mod n) for gcd(M, n) = 1
Thus: M^(e×d) = M^(1 + k×φ(n)) = M × (M^φ(n))^k ≡ M × 1^k ≡ M (mod n)

**3. Security Implications of Private Key Exposure:**

- If d is exposed, any ciphertext can be decrypted: M = C^d mod n
- The confidentiality of all past and future communications using that key pair is compromised
- Anyone can create forged digital signatures using the private key
- Complete authentication and non-repudiation guarantees are lost
- An attacker can impersonate the key owner in digital signature schemes

**4. Why OAEP Padding is Essential:**

**Vulnerabilities of Deterministic Raw RSA:**

- **Deterministic Nature:** Same plaintext always produces same ciphertext, enabling pattern analysis
- **Chosen Ciphertext Attacks:** Attacker can manipulate ciphertexts to extract information (Bleichenbacher attack)
- **Partial Information Leakage:** Specific properties of M can be inferred from C without decryption
- **Textbook RSA Weakness:** No randomization means no semantic security

**OAEP (Optimal Asymmetric Encryption Padding):**
- Introduces randomness through seed and padding bits
- Creates multiple valid ciphertexts for the same plaintext
- Resistant to chosen ciphertext attacks
- Provides semantic security
- Required for secure RSA implementations (PKCS#1 v2.1 standard)

---

## Question 3: AES Modes of Operation and Security Analysis

**Question:**
AES supports multiple modes of operation: ECB, CBC, CTR, GCM, etc.

1. Explain why Electronic Codebook (ECB) mode is considered insecure, provide a real-world example.
2. How does Cipher Block Chaining (CBC) mode overcome ECB's limitations? Include the initialization vector (IV) role.
3. Compare Counter (CTR) mode with CBC mode in terms of parallelization capability and error propagation.
4. Why is GCM (Galois/Counter Mode) preferred for authenticated encryption in modern applications?

**Answer:**

**1. ECB (Electronic Codebook) Mode Insecurity:**

**Mathematical Definition:** C_i = E_K(P_i) for each plaintext block P_i

**Critical Vulnerabilities:**

- **Identical Plaintext Blocks:** Same plaintext block always encrypts to same ciphertext block
- **Pattern Preservation:** Patterns in plaintext are preserved in ciphertext
- **Famous Example - "ECB Penguin":** When encrypting an image file with ECB mode, the image outline remains visible in encrypted data because identical color blocks encrypt identically

**Real-World Impact:**
- Leaks information about plaintext without decryption
- Enables frequency analysis attacks
- Unsuitable for streaming data or large files
- **NEVER used in production security systems**

**2. CBC (Cipher Block Chaining) Mode:**

**Mathematical Definition:** C_i = E_K(P_i ⊕ C_{i-1}), where C_0 = IV

**How CBC Overcomes ECB:**

- **Chaining Mechanism:** Each ciphertext block depends on all previous blocks
- **Identical Plaintexts → Different Ciphertexts:** Same plaintext block produces different ciphertext if previous ciphertext differs
- **Randomization via IV:** Initialization Vector (IV) is random and transmitted with ciphertext (doesn't need to be secret, must be unpredictable)
- **Propagation:** One bit change in P_i affects C_i and all subsequent blocks
- **Semantic Security:** Satisfies semantic security definition

**IV Requirements:**
- Must be random and unpredictable (not sequential or derived from key)
- Fresh IV for each encryption operation
- Can be transmitted in plaintext; secrecy not required

**3. CTR vs CBC Mode Comparison:**

| Feature | CBC | CTR |
|---------|-----|-----|
| **Block Dependency** | Sequential (P_i depends on C_{i-1}) | Independent (counter function) |
| **Parallelization** | Not parallelizable (sequential decryption) | Fully parallelizable (encrypt counter independently) |
| **Error Propagation** | Single-bit error in C_i corrupts P_i and P_{i+1} | Error affects only corresponding block |
| **Latency** | Higher (sequential processing) | Lower (parallel processing) |
| **Stream Cipher** | Block cipher mode | Converts to stream cipher |
| **Random Access** | No random access to blocks | Direct access to any block |

**CTR Mode Formula:** C_i = P_i ⊕ E_K(nonce \|\| counter_i)

**Use Cases:**
- CBC: Traditional applications, sequential data
- CTR: High-performance computing, parallel systems, low-latency requirements

**4. GCM (Galois/Counter Mode) - Authenticated Encryption:**

**GCM Advantages:**

- **Dual Security:** Provides both confidentiality (CTR mode) and authenticity (Galois field multiplication)
- **Authenticated Encryption with Associated Data (AEAD):** Can authenticate unencrypted associated data
- **Parallelizable:** Unlike CBC, allows parallel processing
- **Efficient Tag Generation:** Uses Galois field arithmetic for fast authentication tag computation
- **NIST Recommendation:** Approved for all key sizes
- **Low Overhead:** Single pass for both encryption and authentication

**Mathematical Foundation:**
- Encrypts with CTR mode: C = P ⊕ E_K(counter)
- Computes authentication tag: T = GHASH_H(A \|\| C) where H = E_K(0)
- Tag prevents tampering and ensures authenticity

**Production Security Benefits:**
- Detects active attacks and ciphertext modification
- Prevents padding oracle attacks (no padding needed)
- Non-malleability (cannot create valid ciphertext without knowing key)
- Used in TLS 1.2/1.3, WPA3, IPsec, and industry standard protocols

---

## Question 4: Hash Functions - SHA Family and Security Properties

**Question:**
Hash functions like SHA-1, SHA-256, and SHA-512 are critical security components.

1. Explain the three fundamental properties of cryptographic hash functions: preimage resistance, second preimage resistance, and collision resistance.
2. Why has SHA-1 been deprecated despite its widespread historical use? Explain the SHAttered attack (2017).
3. Describe the internal structure of SHA-256 including Merkle-Damgård construction.
4. How are hash functions used in digital signatures, password storage, and blockchain, with security implications for each?

**Answer:**

**1. Three Fundamental Hash Function Properties:**

**A. Preimage Resistance (One-way Function):**
- Given hash H(x), computationally infeasible to find original input x
- Formal: For all x, h(x) is easy to compute, but given h, finding x is hard
- Brute force attack requires ~2^n operations for n-bit hash
- Ensures passwords cannot be recovered from their hashes

**B. Second Preimage Resistance (Weak Collision Resistance):**
- Given message x and its hash H(x), computationally infeasible to find different message x' where H(x) = H(x')
- Formal: For all x and given H(x), finding x' ≠ x with H(x) = H(x') is hard
- Brute force requires ~2^n operations
- Ensures integrity verification: if H(x) matches stored value, message is authentic

**C. Collision Resistance (Strong Collision Resistance):**
- Computationally infeasible to find ANY two messages x ≠ x' where H(x) = H(x')
- Formal: Finding x, x' with x ≠ x' and H(x) = H(x') is hard
- Brute force (Birthday Attack) requires ~2^(n/2) operations by birthday paradox
- Critical for digital signatures and file integrity verification

**Security Levels:**
- Breaking preimage resistance: 2^n operations
- Breaking second preimage: 2^n operations  
- Breaking collision resistance: 2^(n/2) operations (birthday paradox)

**2. SHA-1 Deprecation and SHAttered Attack:**

**Historical Context:**
- SHA-1 produces 160-bit hash (2^160 security strength)
- Published by NIST in 1995
- Collisions theoretically possible at 2^80 operations (feasible with significant computing power)

**SHAttered Attack (February 23, 2017):**
- Google security researchers demonstrated practical SHA-1 collision
- Created two different PDF files with identical SHA-1 hash
- Required approximately 2^63 SHA-1 operations (~6,500 GPU-years of computation)
- Proved collision resistance theoretically weak, not just theoretical

**Implications:**
- Git repositories using SHA-1 identifiers became vulnerable
- Digital signatures with SHA-1 can be forged
- SSL/TLS certificates with SHA-1 signatures no longer trusted
- **Current Standards:** SHA-1 officially deprecated (FIPS 180-4 superseded by SHA-2/SHA-3)
- **Recommendation:** Migrate to SHA-256 (256-bit, 2^128 collision resistance) or SHA-3

**Why Collision Found:**
- Mathematical weaknesses in SHA-1's message schedule
- Linear relationships in expansion functions
- Differential attack paths identified
- Requires crafted initial messages to exploit these weaknesses

**3. SHA-256 Internal Structure:**

**Merkle-Damgård Construction:**

1. **Message Preprocessing:**
   - Append '1' bit to message
   - Pad with '0' bits until length ≡ 448 (mod 512)
   - Append 64-bit big-endian message length
   - Total length is multiple of 512 bits

2. **Initialize Hash Values (First 32 bits of fractional parts of square roots of first 8 primes):**
   - H0 = 0x6a09e667
   - H1 = 0xbb67ae85
   - ... (8 values total)

3. **Process Each 512-bit Block:**
   - Expand block into 64 32-bit words using:
     - W[i] = σ1(W[i-2]) ⊕ W[i-7] ⊕ σ0(W[i-15]) ⊕ W[i-16] for i = 16 to 63
   - Initialize working variables: a, b, c, d, e, f, g, h with current hash values
   - 64 compression rounds with bit operations and modular addition
   - Update hash values: H_i = H_i + (updated working variables)

4. **Final Output:**
   - Concatenate H0 || H1 || ... || H7 to produce 256-bit hash

**Security Properties:**
- Every input bit affects multiple output bits (avalanche effect)
- Nonlinear operations prevent algebraic attacks
- Expansion function provides diffusion
- Compression function ensures collision resistance

**4. Hash Functions in Real-World Applications:**

**A. Digital Signatures:**
- **Process:** Sign H(message) instead of entire message (efficiency)
- **Security:** Collision resistance critical; two messages with same hash would have same signature
- **Risk if Weak Hash:** SHAttered attack enables signature forgery
- **Application:** ECDSA, RSA-PSS with SHA-256

**B. Password Storage:**
- **Process:** Store H(password + salt) instead of plaintext password
- **Security:** Preimage resistance ensures password cannot be recovered
- **Threat:** Rainbow table attacks (mitigated by salt)
- **Modern Standard:** bcrypt, Argon2 (memory-hard) preferred over plain SHA-256
- **Weakness of Plain Hash:** GPU/ASIC acceleration makes password cracking feasible at rate of billions/second

**C. Blockchain/Cryptocurrency:**
- **Bitcoin Process:** Block header hash using SHA-256(SHA-256(block_data))
- **Security Requirements:**
  - Preimage resistance: Impossible to find block data for given hash (prevents block forgery)
  - Collision resistance: Cannot create different blocks with same hash (prevents double-spend)
  - Proof-of-Work: Find nonce where SHA-256(block + nonce) < target (requires ~2^32 average attempts)
- **51% Attack Prevention:** Collision resistance ensures attacker cannot fork history
- **Application:** Every block linked to previous via hash creates immutable chain

**Comparative Security Analysis:**

| Property | Req. for Sig | Req. for Password | Req. for Blockchain |
|----------|--------------|-------------------|-------------------|
| Preimage Resistance | High | Critical | Critical |
| Second Preimage | Medium | Medium | High |
| Collision Resistance | **Critical** | Low | **Critical** |

---

## Question 5: Cryptographic Protocols - SSL/TLS Handshake Analysis

**Question:**
SSL/TLS is fundamental for secure web communication. Analyze the TLS 1.2 handshake process:

1. Describe the complete TLS 1.2 Handshake protocol with all message exchanges (ClientHello, ServerHello, Certificate, etc.).
2. Explain the role of pre-shared keys, session resumption, and perfect forward secrecy (PFS).
3. What vulnerabilities existed in SSL 3.0 and TLS 1.0/1.1 that forced their deprecation?
4. How does TLS 1.3 improve upon TLS 1.2 in terms of security and performance?

**Answer:**

**1. TLS 1.2 Complete Handshake Protocol:**

**Phase 1: ClientHello**
- Client sends supported TLS versions (e.g., 1.0-1.2), cipher suites, compression methods
- Includes random 32-byte number (ClientRandom: timestamp + 28 random bytes)
- Server name indication (SNI) for virtual hosting
- Supported elliptic curves and signature algorithms

**Phase 2: ServerHello**
- Server selects TLS version, cipher suite, and compression method
- Sends ServerRandom (32 bytes: timestamp + random)
- Session ID for potential resumption (optional)
- Certificate containing server's public key and identity
- Server certificate chain for validation

**Phase 3: ServerKeyExchange (Optional)**
- Only if using Diffie-Hellman key exchange (not RSA)
- Sends DH parameters and digitally signed DH public key
- Ensures server authenticity during key exchange

**Phase 4: ServerHelloDone**
- Signals end of server's handshake messages

**Phase 5: ClientKeyExchange**
- Client generates PreMasterSecret (PMS)
- Encrypts PMS with server's public key and sends
- Server decrypts using private key to obtain PMS

**Phase 6: Key Derivation**
- Both client and server compute:
  - MasterSecret = PRF(PreMasterSecret, "master secret", ClientRandom + ServerRandom)
  - Session keys derived from MasterSecret for encryption/MAC operations

**Phase 7: ChangeCipherSpec**
- Client sends signal to switch to encrypted communication
- All subsequent messages encrypted with negotiated cipher suite

**Phase 8: Finished**
- Client sends encrypted verification hash of all handshake messages
- Server verifies integrity and authenticity
- Server sends its own ChangeCipherSpec and Finished message
- Communication now fully encrypted and authenticated

**2. Session Resumption and Perfect Forward Secrecy:**

**Session Resumption (Abbreviated Handshake):**
- **Session ID Method:** Server stores session info and sends ID to client
  - Client can resume using stored MasterSecret and SessionID
  - Reduces handshake from ~6 round trips to 2
  - Risk: Server must securely store session state
  - Vulnerability: Single breach compromises multiple sessions

- **Session Tickets (TLS 1.2+):** 
  - Server encrypts session state and sends as ticket to client
  - Client stores encrypted ticket, no server state needed
  - Client sends ticket in ClientHello to resume
  - Requires TLS ticket key rotation

**Perfect Forward Secrecy (PFS):**
- **Concept:** Compromise of long-term keys doesn't compromise past session keys
- **Ephemeral Diffie-Hellman (DHE):**
  - Fresh DH private key generated for each session
  - Even if server's long-term key compromised, past sessions remain secure
  - Requires computational overhead for DH parameter generation
  
- **Elliptic Curve Diffie-Hellman (ECDHE):**
  - Uses elliptic curves for faster key exchange while maintaining PFS
  - Industry standard for modern TLS implementations

**Security Benefit:** Even if server private key stolen years later, historical communications cannot be decrypted retroactively (session keys not derivable from server key alone)

**3. SSL 3.0 / TLS 1.0-1.1 Vulnerabilities:**

**SSL 3.0 Vulnerabilities:**

- **POODLE Attack (Padding Oracle On Downgrade Legacy Encryption):** 
  - Forced downgrade to SSL 3.0 and exploited padding oracle
  - Attacker could decrypt traffic despite encryption

**TLS 1.0 / 1.1 Vulnerabilities:**

| Vulnerability | Description | Impact |
|---------------|-------------|--------|
| **BEAST** | Exploit of IV prediction in CBC mode | Plaintext recovery attacks |
| **Heartbleed** | Buffer overflow in OpenSSL heartbeat extension | Memory disclosure including session keys |
| **Padding Oracle** | CBC padding verification timing leaks | Ciphertext decryption without keys |
| **Weak Ciphers** | Support for DES, RC4, MD5 | Practical break attacks (RC4 bias) |
| **Client Renegotiation Injection** | Attacker could inject commands during renegotiation | Request forgery and injection attacks |

**Deprecation Timeline:**
- SSL 3.0: Deprecated 2014 (POODLE)
- TLS 1.0/1.1: Deprecated January 1, 2020 (Browser and server providers mandatory)
- Reason: Fundamental design flaws and accumulated vulnerabilities

**4. TLS 1.3 Improvements:**

**Security Enhancements:**

1. **Mandatory PFS:** All cipher suites use ephemeral key exchange (DHE/ECDHE mandatory)
   - Previous TLS versions allowed static RSA key exchange (no forward secrecy)

2. **Removed Weak Algorithms:**
   - Eliminated RSA key transport (replaced with Diffie-Hellman)
   - Removed RC4, DES, 3DES, MD5 support
   - Only strong AEAD ciphers (ChaCha20-Poly1305, AES-GCM)

3. **0-RTT (Zero Round Trip Time):**
   - Client sends encrypted data in first message using pre-shared key
   - Server can respond immediately without handshake completion
   - Reduces connection latency significantly
   - Requires careful implementation to prevent replay attacks

**Performance Improvements:**

1. **Faster Handshake:**
   - TLS 1.2: Full handshake = 2 round trips minimum
   - TLS 1.3: Full handshake = 1 round trip (ClientHello + ServerHello with encrypted data sent)
   - Resumption: 0 round trips with 0-RTT

2. **Simplified Message Flow:**
   - Removed ChangeCipherSpec and deprecated extensions
   - Cleaner message ordering
   - Fewer cryptographic operations

3. **Session Resumption:**
   - Pre-shared keys (PSK) replace session tickets
   - Post-handshake authentication optional
   - Bandwidth reduction ~30-40%

**Architectural Changes:**
- **Handshake Encryption:** Handshake messages encrypted after ServerHello (TLS 1.2: only record layer encrypted)
- **Early Data (0-RTT):** Client can send application data before handshake completion
- **Key Derivation:** Uses HKDF (HMAC-based Extract-and-Expand Key Derivation Function) for more robust key material generation

**Real-World Impact:**
- Average HTTPS connection time reduced from ~100ms to ~50ms
- Mobile connections (high latency) see 20-30% improvement
- Industry adoption: 95%+ of major websites (2025)

---

## Question 6: Digital Signatures and Public Key Infrastructure (PKI)

**Question:**
Digital signatures are critical for non-repudiation and authenticity. Answer comprehensively:

1. Explain the mathematical process of creating and verifying digital signatures using RSA-PSS (RSA Padding Scheme).
2. What advantages do Elliptic Curve Digital Signature Algorithm (ECDSA) and EdDSA provide over RSA?
3. Describe the role of Certificate Authority (CA), Root Certificate, Intermediate Certificate, and Certificate Chain validation.
4. Explain the risks of improper certificate validation and provide real-world attack examples (e.g., Diginotar, bad certificate imports).

**Answer:**

**1. RSA-PSS Digital Signatures:**

**Signature Creation Process:**

**Step 1: Hash Message**
- m = Hash(message) using SHA-256

**Step 2: Prepare with EMSA-PSS-ENCODE**
- Generate random salt (typically 32 bytes for SHA-256)
- Compute: m' = Hash(8 zero bytes || m || salt)
- Create padded message: EM = (0x00 || m' || salt || 0xBC) with bytes prepended for block length

**Step 3: RSA Sign Operation**
- Signature S = EM^d mod n (where d is private exponent, n is modulus)

**Signature Verification Process:**

**Step 1: RSA Verification Operation**
- EM = S^e mod n (where e is public exponent)

**Step 2: Verify Format**
- Check last byte is 0xBC
- Extract m' and salt from EM

**Step 3: Recompute Hash**
- m'_check = Hash(8 zero bytes || m || salt)

**Step 4: Validate**
- If m' == m'_check, signature is valid
- If not, signature is forged or tampered

**Security Properties:**

- **Deterministic but Unforgeable:** Same message with same salt produces same signature (ideal for non-repudiation)
- **Salt Randomization:** Different signatures for same message when using fresh salt (prevents pattern analysis)
- **Collision Resistance:** Requires strong hash function; forging requires finding hash collision
- **Padding Oracle Protection:** OAEP-like padding prevents certain attacks

**2. ECDSA and EdDSA Advantages Over RSA:**

**Elliptic Curve Digital Signature Algorithm (ECDSA):**

| Aspect | RSA | ECDSA |
|--------|-----|-------|
| **Key Size** | 2048-4096 bits | 256-521 bits |
| **Signature Size** | 256-512 bytes | 64-132 bytes |
| **Computation Speed** | Slower (large exponent) | Faster (elliptic curve math) |
| **Patent Status** | Broadly licensed | Patent issues resolved (2015) |
| **Security Level** | 112-bit (2048-bit RSA) | 256-bit (521-bit ECDSA) |

**ECDSA Advantages:**
- **Smaller Keys:** 256-bit ECDSA ≈ 3072-bit RSA security equivalent
- **Shorter Signatures:** ~64 bytes vs ~256 bytes for RSA
- **Faster Operations:** Elliptic curve operations cheaper than modular exponentiation
- **Bandwidth Efficiency:** Ideal for IoT, mobile, and blockchain applications

**Edwards-Curve Digital Signature Algorithm (EdDSA):**

**Advantages Over Both RSA and ECDSA:**
- **Deterministic Signing:** No randomness required (no entropy source needed)
- **Preimage Resistance:** Uses SHA-512 internally for stronger hash
- **Batch Verification:** Multiple signatures can be verified simultaneously
- **Simpler Implementation:** Fewer edge cases, harder to implement insecurely
- **Consistent Performance:** Same time regardless of input (prevents timing attacks)
- **Non-malleable:** Cannot create valid alternate signatures

**Ed25519 (Edwards on Curve25519):**
- 256-bit security level
- 64-byte signatures
- 32-byte keys
- Used in SSH, PGP, DNSSEC, Signal protocol

**Security Comparison:**
- ECDSA(P-256) vulnerable to side-channel attacks if not carefully implemented
- EdDSA naturally constant-time, side-channel resistant
- EdDSA preferred for cryptographic protocols requiring high assurance

**3. Public Key Infrastructure (PKI) Components:**

**Certificate Authority (CA) - Hierarchy:**

**Root CA:**
- Self-signed certificate (signs its own certificate)
- Private key highly protected (often offline/air-gapped)
- Validity typically 20+ years
- Embedded in operating systems and browsers
- Used to verify intermediate CAs only

**Intermediate CA:**
- Issued and signed by Root CA
- Used to sign end-entity (server) certificates
- Acts as buffer; compromise doesn't directly compromise root
- Revocation of intermediate certificate revokes all subordinate certificates
- Validity typically 10 years

**End-Entity (Leaf) Certificate:**
- Issued by Intermediate CA for specific domain/server
- Contains:
  - Server public key
  - Server identity (Common Name, Subject Alternative Names)
  - Validity period (typically 1-2 years, browser limit 398 days since 2020)
  - Key usage restrictions (signatures, encryption)
  - Issuer information

**Certificate Chain Validation Process:**

```
Client validates www.example.com certificate:

1. Verify Leaf Certificate signature using Intermediate CA public key
2. Verify Intermediate CA certificate using Root CA public key
3. Verify Root CA certificate using pre-installed root public key
4. Check validity dates on all certificates (current time in valid range)
5. Check Certificate Revocation List (CRL) or OCSP for revocation status
6. Verify certificate key usage allows TLS server authentication
7. Verify domain matches certificate Subject/SAN
```

**4. Certificate Validation Risks and Attack Examples:**

**Improper Validation Vulnerabilities:**

**1. DigiNotar Breach (2011):**
- **Incident:** Hacker obtained CA private key and issued fraudulent certificates
- **Rogue Certificates:** Created certificates for Google, Microsoft, Yahoo, etc.
- **Attack Vector:** MITM attacks on any user connecting to https://gmail.com (appeared legitimate)
- **Consequence:** DigiNotar certificate authority trust revoked worldwide
- **Lesson:** CA security is critical; system relies on CA trustworthiness

**2. Comodo CA Breach (2011):**
- Attacker registered infrastructure.comodo.com and obtained certificate from Comodo's registration authority
- Issued certificates for Gmail, Yahoo, Hotmail allowing MITM attacks
- **Fix:** Comodo revoked certificates; tighter domain validation implemented

**3. Bad Certificate Import (Android 4.4 and Earlier):**
- Root certificate "master key" discovered in Android firmware
- Allowed installation of arbitrary root certificates
- Any HTTPS connection could be MITM'd if attacker obtains system privileges
- **Fix:** Android 5.0+ restricts certificate store permissions

**Validation Mistakes:**

| Mistake | Consequence | Example |
|---------|-------------|---------|
| Not verifying chain to trusted root | Attacker can use self-signed cert as "trusted" | Accepting any certificate as valid |
| Ignoring certificate expiration | Compromised keys can be used indefinitely | Using expired certificates |
| Not checking revocation status | Stolen/compromised certs still accepted | Not checking CRL/OCSP |
| Hostname mismatch not validated | MITM with certificate for different domain | www.example.com cert used for www.attacker.com |
| Accepting self-signed for critical services | MITM attack on first connection | SSH known_hosts without verification |

**Modern Mitigation:**

- **Certificate Transparency:** All publicly trusted certificates logged in append-only logs
- **OCSP Stapling:** Server provides signed revocation status with certificate (faster, privacy-preserving)
- **HPKP/HSTS:** Preload lists prevent MITM for known domains
- **CAA Records:** DNS records specify which CAs can issue certificates for domain
- **Certificate Pinning:** Applications pin expected certificate or public key (mobile apps)

---

## Question 7: Key Exchange Protocols and Diffie-Hellman

**Question:**
Key exchange is fundamental to establishing secure communications. Analyze Diffie-Hellman and modern variants:

1. Explain the Diffie-Hellman (DH) mathematical process, including why the protocol is secure against passive adversaries.
2. Why is basic Diffie-Hellman vulnerable to man-in-the-middle (MITM) attacks, and how are attacks prevented in TLS?
3. Compare Diffie-Hellman, Elliptic Curve Diffie-Hellman (ECDH), and Elliptic Curve Diffie-Hellman Ephemeral (ECDHE) in terms of security and performance.
4. Explain the Logjam attack on Diffie-Hellman and why 2048-bit or larger primes are now mandatory.

**Answer:**

**1. Diffie-Hellman Mathematical Process:**

**Protocol Steps:**

**Public Parameters (agreed beforehand or included in handshake):**
- Large prime p (typically 2048-4096 bits)
- Generator g (1 < g < p), where g^(p-1) ≡ 1 (mod p)

**Alice's Side:**
- Selects random private key a (1 < a < p-1)
- Computes public key A = g^a mod p
- Sends A to Bob (public, anyone can see)

**Bob's Side:**
- Selects random private key b (1 < b < p-1)
- Computes public key B = g^b mod p
- Sends B to Alice

**Shared Secret Derivation:**

**Alice:** K = B^a mod p = (g^b)^a mod p = g^(ab) mod p
**Bob:** K = A^b mod p = (g^a)^b mod p = g^(ab) mod p

**Both derive identical shared secret K = g^(ab) mod p**

**Security Against Passive Adversary:**

An eavesdropper observes: p, g, A = g^a mod p, B = g^b mod p

To compute K = g^(ab) mod p, attacker must determine a or b from A or B:

**Discrete Logarithm Problem:** Given g^x mod p, find x is computationally hard (no known polynomial algorithm)

- Best known algorithm: General Number Field Sieve (GNFS)
- Time complexity: L(p) = exp((log p)^(1/3) * (log log p)^(2/3))
- For 2048-bit p: ~2^110 operations (infeasible with current technology)

**Computational Diffie-Hellman Assumption:**
- Even knowing g^a, g^b mod p, computing g^(ab) mod p is hard
- Passive eavesdropper cannot derive shared secret

**2. MITM Vulnerability and TLS Prevention:**

**Basic DH MITM Attack:**

```
Secure Channel Desired:
Alice <--[shared key]--> Bob

Attacker Intercepts:
Alice <--[shared key with Eve]--> Eve <--[shared key with Eve]--> Bob

Eve computes:
- With Alice: K_A = B_eve^a mod p (Eve knows her private key)
- With Bob: K_B = B_bob^e mod p (Eve knows her private key)

Alice thinks she shares key with Bob, but communicates with Eve.
Eve decrypts messages from Alice (using K_A), re-encrypts with Bob's key (using K_B).
Complete compromise of confidentiality and integrity.
```

**Why Occurs:** DH exchange has no authentication. Any value can be sent as public key; receiver doesn't verify sender identity.

**TLS 1.2 Prevention Methods:**

**1. Certificate-Based Authentication (DHE_RSA):**
- Server signs DH parameters: Sign(g, p, B) using server's private key
- Server sends certificate containing public key for signature verification
- Client verifies:
  - Certificate chain leads to trusted root CA
  - Certificate validity period, key usage, hostname match
  - Signature over DH parameters using server's public key
- Prevents attacker from substituting DH parameters (would need server's private key)

**2. Pre-established Trust:**
- Client has already verified server identity via certificate
- Client knows server's legitimate public key
- Client can verify DH parameters signed by this key

**TLS 1.3 Enhanced Security:**
- Mandatory signature on all handshake messages
- HMAC-based authentication (Message Authentication Code)
- Post-handshake authentication
- Prevents any parameter substitution

**3. Elliptic Curve Diffie-Hellman Comparison:**

**Diffie-Hellman (DH):**
- **Group:** Multiplicative group modulo prime p
- **Key Size:** 2048-4096 bits for adequate security
- **Computation:** Modular exponentiation (relatively slow)
- **Patent Status:** Expired (2000), no licensing concerns

**Elliptic Curve Diffie-Hellman (ECDH) Static:**
- **Curve:** y² = x³ + ax + b (mod p)
- **Operation:** Point multiplication [k]P on elliptic curve
- **Key Size:** 256-521 bits equivalent to 2048-15360-bit DH
- **Computation:** Scalar multiplication (faster than modular exponentiation)
- **Forward Secrecy:** No; uses static key pairs

**ECDHE (Ephemeral ECDH):**
- **Fresh Key Pair:** Generated for each session
- **Key Size:** Same as ECDH (256-521 bits)
- **Computation:** Same as ECDH (faster than DH)
- **Forward Secrecy:** Yes; session compromise doesn't affect other sessions
- **Modern Standard:** Default for TLS 1.2/1.3

**Comparative Performance:**

| Metric | 2048-bit DH | 256-bit ECDH | 4096-bit DH |
|--------|------------|-------------|------------|
| **Key Generation** | ~0.5-1ms | ~0.1ms | ~5-10ms |
| **Shared Secret Derivation** | ~0.5-1ms | ~0.1-0.2ms | ~5-10ms |
| **Key Size** | 2048 bits | 256 bits | 4096 bits |
| **Security Level** | 112 bits | 128 bits | 128 bits |

**ECDHE Clear Winner:** 10-100x faster with better security

**4. Logjam Attack and Modern Requirements:**

**Logjam Attack (May 2015):**

**Vulnerability:** Attacker could downgrade TLS connection to use 512-bit export-grade primes.

**Attack Mechanism:**

1. **Export Ciphers Legacy:** TLS allowed 512-bit primes for "export" compliance with 1990s U.S. regulations
2. **Downgrade Attack:** Attacker modifies ClientHello to remove non-export cipher suites
3. **Weak Prime Negotiation:** Server forced to use 512-bit DH prime
4. **Discrete Log Computation:** Attacker precomputes discrete log tables for common 512-bit primes:
   - NFS (Number Field Sieve) can break 512-bit DH in hours/days
   - ~2^60 operations feasible with GPU clusters
5. **Session Compromise:** Attacker computes shared secret K and decrypts traffic

**Why 2048-bit Mandatory Now:**

- **Security Estimate:** 512-bit DH provides only ~64 bits of security (GNFS complexity ~2^64)
- **Practical Feasibility:** Logjam researchers broke 512-bit DH in minutes (precomputation)
- **1024-bit DH:** Estimates suggest ~80 bits security, breakable with nation-state resources
- **2048-bit DH:** ~112 bits security, infeasible attack even for well-resourced adversaries
- **Modern Standard:** TLS 1.2+ requires minimum 2048-bit primes; 4096-bit recommended for critical applications

**Recommendations:**
- Disable export-grade ciphers entirely (broken security model)
- Use ECDHE over DHE (faster, stronger with smaller keys)
- Minimum 256-bit elliptic curves (equivalent ~128-bit security to 2048-bit DH)

---

## Question 8: Hash Functions and Message Authentication Codes (MACs)

**Question:**
Hash functions and MACs are critical for message integrity and authentication:

1. Explain the construction and security properties of HMAC (Hash-based Message Authentication Code) and how it prevents length extension attacks unlike simple Hash(key || message).
2. Compare HMAC-SHA256 with CMAC (Cipher-based MAC) and GMAC (Galois Message Authentication Code) in terms of security, performance, and cryptographic soundness.
3. Describe how authenticated encryption modes like GCM and ChaCha20-Poly1305 combine confidentiality and authenticity, and why this prevents tampering attacks.
4. Explain timing attacks on MAC verification and how constant-time comparison prevents information leakage.

**Answer:**

**1. HMAC Construction and Security:**

**HMAC Formula:**

```
HMAC(K, m) = H(K_outer ⊕ opad || H(K_inner ⊕ ipad || m))
```

**Where:**
- K = secret key (padded to hash block size)
- H = cryptographic hash function (e.g., SHA-256)
- opad = 0x5C repeated for block size (outer padding constant)
- ipad = 0x36 repeated for block size (inner padding constant)
- m = message

**Construction Steps:**

**Step 1: Key Preprocessing**
- If len(K) > block size: K = H(K)
- Pad K with zeros to block size: K_padded

**Step 2: Inner Hash**
- K_inner = K_padded ⊕ ipad (XOR with inner padding)
- Inner = H(K_inner || m)

**Step 3: Outer Hash**
- K_outer = K_padded ⊕ opad (XOR with outer padding)
- HMAC = H(K_outer || Inner)

**Security Properties:**

**1. Length Extension Attack Prevention:**

**Problem with Hash(K || M):**
- Attacker knowing Hash(K || M) can compute Hash(K || M || X) without knowing K
- Why: Hash function produces state after processing M; attacker can continue hashing from that state
- Vulnerability: Attacker extends message and produces valid MAC

**HMAC Immunity:**
- Outer hash layer prevents this: attacker cannot produce valid outer hash without K
- Even if attacker computes inner hash for extended message, outer layer requires K knowledge
- Nested hash structure fundamentally prevents length extension

**2. Key Separation (ipad/opad):**
- Inner and outer functions use different key material (ipad vs opad)
- Prevents related-key attacks where attacker manipulates key derivation
- Forces two independent hash computations

**3. Strong as Underlying Hash:**
- If H is collision-resistant, HMAC is forgery-resistant
- Security proof: HMAC(K, m) forgery requires breaking H
- Recommended: Use with SHA-256 or SHA-512

**HMAC(SHA-256) Output:**
- Same size as SHA-256: 256 bits (32 bytes)
- Typically used as 256-bit MAC (full output) or truncated (128 bits minimum)

**2. HMAC vs CMAC vs GMAC Comparison:**

**HMAC (Hash-based MAC):**

```
HMAC-SHA256 = H((K ⊕ opad) || H((K ⊕ ipad) || message))
```

**Advantages:**
- Fast (hash functions highly optimized)
- Wide key size flexibility
- Well-analyzed security
- Hardware acceleration available (SHA-NI)

**Disadvantages:**
- Hash-dependent (slower for some hashes)
- Not an "authenticated encryption" (only authentication)
- Output size fixed to hash size

**CMAC (Cipher-based MAC):**

```
CMAC[T] = T(L || message) where T uses AES

K1 = LSHIFT(E_K(0)) with conditional XOR if overflow
```

**Advantages:**
- Provably secure under AES security assumptions
- No length extension attacks (block cipher strength)
- Efficient with block cipher acceleration
- Output size fully customizable

**Disadvantages:**
- Requires AES encryption (not just hashing)
- Two key derivations (more complex)
- Slower than HMAC for large messages on non-AES hardware

**GMAC (Galois Message Authentication Code - GCM's authentication component):**

```
GMAC(K, IV, A) = GCM's authentication tag without encryption
```

**Advantages:**
- Parallelizable (operates on Galois field arithmetic)
- Optional encryption (can use just for authentication)
- Extremely fast for high throughput
- Hardware support in modern CPUs (PCLMULQDQ)

**Disadvantages:**
- Security deteriorates with IV reuse (must never reuse)
- Complex implementation (Galois field arithmetic)
- Not suitable for variable-size tags (standard 128-bit)
- Dependent on GCM mode definition

**Performance Comparison (per 1GB message):**

| MAC | Time | Throughput |
|-----|------|-----------|
| HMAC-SHA256 | ~100ms | ~10GB/s |
| CMAC-AES128 | ~200ms | ~5GB/s |
| GMAC (with AES-NI) | ~50ms | ~20GB/s |

**Cryptographic Soundness:**

- **HMAC:** Proven secure if H is pseudorandom function
- **CMAC:** Proven secure if AES is pseudorandom permutation
- **GMAC:** Proven secure if AES secure and IV never reused

**Practical Choice:**
- TLS 1.2: HMAC-SHA256 (standard, broadly compatible)
- TLS 1.3: HMAC or HKDF (HMAC-based key derivation)
- Authenticated Encryption: GCM or ChaCha20-Poly1305 (AEAD)

**3. Authenticated Encryption: GCM and ChaCha20-Poly1305:**

**GCM (Galois/Counter Mode):**

**Process:**

1. **Encryption (CTR mode):**
   - Generate counter blocks: inc(nonce || counter)
   - C_i = P_i ⊕ AES(K, counter_i)
   - Produces ciphertext C

2. **Authentication (Galois Field Arithmetic):**
   - Compute: T = GF_mult(C[0], H^n) ⊕ ... ⊕ GF_mult(C[n-1], H) ⊕ GF_mult(len, H)
   - H = AES(K, nonce || 0x0...01)

3. **Tag Generation:**
   - MAC_tag = AES(K, nonce || 0x0...02) ⊕ T

**Tampering Protection:**
- Attacker changes ciphertext bit → changes T → changes MAC_tag
- Verification fails unless attacker knows K
- Cannot guess valid tag (256-bit security with 128-bit tag)

**ChaCha20-Poly1305:**

**Process:**

1. **Key Derivation:**
   - One-time key: OTP = ChaCha20(K, nonce, block 0)
   - Encryption key: K_enc = ChaCha20(K, nonce, block 1-...) for streaming

2. **Poly1305 Authentication (Polynomial MAC):**
   - Per-block authentication: Poly1305(r, block_i)
   - r = OTP[0..31] (random, per-message)
   - Computes MAC as polynomial evaluation: ∑ (block_i * r^(n-i)) mod 2^130-5

3. **Tag Generation:**
   - Tag = Poly1305(associated_data || ciphertext || lengths) mod 2^130-5

**Tampering Detection:**
- Single bit modification in ciphertext requires changing polynomial
- Attacker cannot forge valid tag without knowing r (from OTP)
- Cryptographically unforgeable under Poly1305 security model

**Comparative Analysis:**

| Feature | GCM | ChaCha20-Poly1305 |
|---------|-----|-------------------|
| **Underlying Cipher** | AES-CTR + Galois Field | ChaCha20 + Poly1305 |
| **Parallelization** | Parallel CTR blocks | Sequential (Poly1305) |
| **IV Reuse Catastrophe** | Complete compromise | Complete compromise |
| **Hardware Dependency** | AES-NI critical for speed | Software competitive |
| **Constancy** | Deterministic | Deterministic |
| **NIST Approval** | Yes (FIPS 800-38D) | Yes (RFC 7539) |
| **Performance (AES-NI)** | ~1 cycle/byte | N/A |
| **Performance (No AES-NI)** | ~10 cycles/byte | ~2 cycles/byte |

**Attack Prevention:**

- **Tampering Detection:** Any modification caught by authentication tag mismatch
- **Reordering Prevention:** Authentication encompasses entire message including length
- **Key Confusion:** Tag includes nonce; different nonces produce different tags
- **Decryption Hazard:** Tag verified BEFORE decryption (prevents Bleichenbacher-style attacks)

**4. Timing Attacks on MAC Verification:**

**Vulnerable Implementation (INCORRECT):**

```python
def verify_mac(computed_tag, received_tag):
    if computed_tag == received_tag:  # STRING COMPARISON
        return True
    return False
```

**Timing Attack Exploit:**

**Process:**
1. Attacker sends message with guessed MAC tag
2. Server compares byte-by-byte:
   - If first byte matches: comparison takes longer (more bytes checked before failure)
   - If first byte mismatches: comparison fails immediately
3. Attacker measures response time
4. Shorter time → first byte incorrect
5. Longer time → first byte correct
6. Repeat for all 32 bytes to forge valid MAC

**Information Leakage:**
- Timing difference reveals: which bytes are correct
- Attacker progressively learns correct MAC tag
- Expected attempts: 32 × 256 / 2 = 4096 (instead of 2^256)

**Practical Example:**
- Normal comparison: 32 bytes, 1 microsecond per byte = 32μs for full match
- Attacker sends wrong byte 1: comparison fails at byte 1, ~1μs
- Attacker sends correct byte 1: comparison fails at byte 2, ~2μs
- Timing difference detectable with network measurements or local access

**Constant-Time Comparison (CORRECT):**

```python
def constant_time_compare(computed_tag, received_tag):
    if len(computed_tag) != len(received_tag):
        return False
    
    result = 0
    for computed_byte, received_byte in zip(computed_tag, received_tag):
        result |= computed_byte ^ received_byte  # XOR, accumulate differences
    
    return result == 0  # All bits same if equal
```

**Why It Works:**
- Always compares all bytes regardless of matches
- Uses XOR (constant time operation)
- Does not short-circuit on mismatch
- Time depends only on tag length, not matching bytes

**Timing Characteristic:**
- Correct MAC: all bytes processed, ~32μs
- Incorrect MAC: all bytes processed, ~32μs
- No information leakage in timing

**Hardware-Level Constant-Time:**
- Use CPU instructions with no branch prediction dependency
- Avoid conditional jumps based on comparison result
- Ensure cache hits constant (no cache-timing attacks)

**Mitigation Strategies:**

1. **Cryptographic Library Usage:** OpenSSL's OPENSSL_memcmp(), libsodium's sodium_memcmp()
2. **Programming Language:** Rust's built-in timing-safe comparison
3. **Manual Implementation:** Ensure no early exit from loop
4. **Defense-in-Depth:** Rate limiting on failed authentication attempts
5. **Authentication Headers:** Tag verified before processing (prevent partial information extraction)

**Real-World Incident:**
- Apache Commons Collections timing attack allowed padding oracle attacks
- Simple optimization (early exit on mismatch) compromised security
- Lesson: Cryptographic verification must never optimize based on comparison result

---

## End of Assignment-3

**Total Questions:** 8 Medium-Hard Level Questions with Comprehensive Solutions

**Topics Covered:**
- Symmetric Cryptography (DES vs AES)
- Asymmetric Cryptography (RSA)
- Block Cipher Modes (ECB, CBC, CTR, GCM)
- Hash Functions (SHA-1, SHA-256)
- Cryptographic Protocols (SSL/TLS)
- Digital Signatures and PKI
- Key Exchange (DH, ECDH, ECDHE)
- Message Authentication (HMAC, CMAC, GMAC, Authenticated Encryption)
