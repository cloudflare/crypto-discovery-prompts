# Cryptography discovery prompts

These experimental prompts grew out of an internal [Cloudflare research project](https://blog.cloudflare.com/ai-driven-cryptography-discovery/) on AI-assisted discovery of cryptography, with a focus on post-quantum migration.  They help identify uses of asymmetric cryptography in source code and investigate how those uses fit into a system. They are starting points for an investigation, but they are not a standalone version of our internal CryptoLabe tool.

## What's in this repository

- [`01-discovery.md`](prompts/01-discovery.md) surveys one repository and produces raw findings. It does not explain the findings or recommend migrations.
- [`02-deep-analysis.md`](prompts/02-deep-analysis.md) takes one raw finding and investigates its use, dependencies, post-quantum classification, and potential prerequisites. It produces a Markdown report.
- [`hard-cases.md`](prompts/hard-cases.md) is a separate, standalone prompt for less routine migration questions, such as custom protocols, size-constrained signatures, hardware-bound cryptography, and third-party dependencies.

To use the first two prompts, check out the repository you want to inspect at a specific commit. Run discovery (the first prompt) against that repository snapshot, then run analysis (the second prompt) once for each raw finding, using the same repository snapshot. Replace the angle-bracket placeholders in each prompt with the repository name, commit, source URL, and requested finding. Discovery also takes a short uppercase *slug* for finding IDs: with `EXAMPLE`, findings are numbered `EXAMPLE-001`, `EXAMPLE-002`, and so on. The prompts do not require a particular source host or model provider.

We recommend giving the model access to a sandboxed, read-only repository snapshot. The prompts do not ask the model to execute the project's code, install packages, or modify source files. If you wish to allow the analysis prompt to trace findings from the initial repository to related repositories, you could provide it with read-only access to your source control system. If not, the analysis stage should instead output a report that describes dependencies and repositories that it could not inspect

## Status and limitations

These are early experimental prompts. Cloudflare keeps refining them through scans and engineering review, including internal versions tuned to our own infrastructure. We're publishing this as a general baseline so others can learn from our approach and build on it.

- **No guarantee of complete coverage.** The prompts may not find every use of asymmetric cryptography in a codebase, and they can produce both false positives and false negatives. We do not have a ground-truth dataset for measuring complete coverage or reproducibly comparing prompt versions.  Don't use these prompts as your only cryptographic inventory tool or as a substitute for security review. Check the findings yourself.
- **Results depend on the model.** We developed and tested these prompts mainly with open-weight models on Cloudflare Workers AI, all of which had large context windows, function calling and reasoning. Other models, or different settings, may give different results.
- **Your code, your environment.** You're responsible for which models and providers see your source code, and for whether that's appropriate for your organization.
- **No support commitment.** We may update, change or stop maintaining this repository at any time.
- **Developed with AI assistance.** These prompts were developed and iteratively refined using AI tools, under the direction and review of Cloudflare employees

## Run both stages with one orchestration prompt

For an experimental workflow that runs discovery and analysis against a single repository using subagents, see [`QUICKSTART.md`](QUICKSTART.md). Read the limitations above first. The quickstart is not a substitute for a properly sandboxed, read-only workflow, and its results still need engineering review.

## License

This project is provided "AS IS" under the Apache License 2.0, without warranties or conditions of any kind. See [LICENSE](LICENSE), especially Sections 7 and 8.

