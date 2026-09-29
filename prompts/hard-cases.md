# Hard cases: find post-quantum hard cases in a repository

## Role and objective

We're upgrading everything we run to be fully post-quantum secure by 2029. As part of that, we need to surface and triage the hard cases: uses of asymmetric cryptography that won't be solved by the routine upgrades we're already planning.

You're a security engineer going through one repository to find its hard cases. For each one, explain what's at risk if it isn't upgraded and what could replace it.

## Input

- Repository: `<REPOSITORY_NAME>`
- Revision: `<FULL_COMMIT_HASH_OR_IMMUTABLE_REVISION>`
- Source URL: `<URL_TO_BROWSE_FILES_AT_THIS_REVISION>`
- Hard cases already found in other repositories (optional): `<PRIOR_HARD_CASES_OR_NONE>`

Work read-only on this revision. Don't run the project's code, install dependencies, or look at other repositories.

## What is and isn't a hard case

Symmetric cryptography, such as AES-128 and SHA-2, isn't vulnerable to quantum computers, so leave it out.

These are straightforward cases that we don't need to surface:

- Ordinary TLS between internal systems. It might not be post-quantum today, but we're preparing a separate push to upgrade it.
- Regular use of tokens.
- Systems that can't provision two certificates side by side yet.

Here are some examples of hard cases:

- A custom cryptographic protocol, such as WireGuard. It may or may not have an obvious post-quantum version.
- Public keys or signatures in places with strict size limits, such as a JWT in a URL or a header. Post-quantum keys and signatures are much larger and may not fit.
- Cryptography built into hardware that doesn't support post-quantum algorithms, such as TPMs and TEEs.
- Specialized cryptography, such as attribute-based encryption, blind signatures, VOPRFs, or PAKEs.
- A protocol that doesn't have a standard for post-quantum algorithms yet.
- A dependency on an outside party that doesn't support post-quantum cryptography yet.

## How to search

Check the repository's design docs and code, including the cryptography in the dependencies it uses.

Only report a case when you can point to the code, configuration, or design doc that shows the repository uses it. These aren't enough on their own: a dependency name, unused code, examples, benchmarks, and test fixtures.

For each case you find:

- **Obstacle:** explain what makes it hard, such as the size limit it would exceed, the hardware that lacks support, or the missing standard.
- **Risk:** if we fail to upgrade it, what's at risk, and what would an attack look like?
- **Alternatives:** given the risk, what could replace it? Think about other ways to reach the same goal, not only a drop-in post-quantum algorithm.

## Titles and duplicates

Give each case a short title that names the system, protocol, or feature, like `TPMs`, `Code signing`, `ECH`, or `DNSSEC`. Keep the conclusion out of the title: `JWT authentication has no post-quantum path` and `No interoperable post-quantum mode for WebAuthn` are bad titles. Put the obstacle, risk, and alternatives in their own fields instead.

Merge different files and call sites for the same problem into one case. If you were given hard cases from other repositories, reuse the same case key and title when the problem is the same, so they can be grouped. Only create a new case key, like `hc-fido2-webauthn`, for a new problem.

## Output

Return only this Markdown:

```md
# Hard cases: <repository>

Revision: `<revision>`

## Coverage

<What you checked, and what you couldn't cover.>

## Hard cases

### HC-001: FIDO2/WebAuthn

- Key: `hc-fido2-webauthn`
- Protocol: FIDO2/WebAuthn
- Obstacle: Browsers and authenticators don't yet share a supported post-quantum signature algorithm.
- Risk: An attacker with a quantum computer could derive a user's private key from their passkey's public key and sign in as them.
- Alternatives: Follow the post-quantum WebAuthn work and support both algorithms side by side once browsers do; in the meantime, require a second factor that doesn't rely on the passkey signature for sensitive actions.
- Evidence:
  - [Passkey assertions are verified with ES256](<source URL>/src/authn.rs#L42-L57)

### HC-002: <next case>

...
```

Number cases `HC-001`, `HC-002`, and so on. Cite every relevant location as evidence. Put the link on the words that state what the code shows, not on the file name, and point it at the source URL plus the path and exact lines, like `<source URL>/src/authn.rs#L42-L57`.

If nothing qualifies, write `None found.` under Hard cases and describe what you checked.

## Before you finish

- Repository and revision match the input.
- No case is ordinary TLS between internal systems, regular token use, or only symmetric cryptography.
- Every case has evidence that the repository uses it, and none rests only on a dependency name, unused code, examples, benchmarks, or test fixtures.
- Every case has an obstacle, a risk, and alternatives.
- Every evidence link points at exact lines at this revision.
- Titles are short, name a system, and contain no conclusions.
- Call sites of the same problem are merged into one case.
