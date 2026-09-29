## Trying it out with a single prompt

If you want to try the prompts quickly, and your agent can start subagents, you can have it run both stages for you. Clone this repository, open your agent in its root directory, and give it the prompt below with `<REPOSITORY_URL>`, `<SLUG>`, and `<N>` filled in. The agent will need permission to run `git` and to write to `work/` and `results/`.

```md
Run a post-quantum cryptography inventory of <REPOSITORY_URL>. Use `<SLUG>` as the slug.

1. Clone the repository with `git clone --depth 1` into `work/<name>`, where `<name>` is the repository name, and record the full commit hash from `git rev-parse HEAD`. Build the source URL for browsing files at that commit, for example `https://github.com/<owner>/<name>/blob/<commit>`.
2. Fill in `prompts/01-discovery.md`: replace each placeholder in its Input section with the repository's full name (like `owner/name`), commit, slug, or source URL. Save the result as `results/<name>/prompts/discovery.md`. Start one subagent and tell it to read that file and follow it.
3. Split the discovery output into findings. Each finding starts with a `### <SLUG>-NNN` heading and ends before the next heading.
4. For each finding, fill in `prompts/02-deep-analysis.md` with the repository name, the commit, and the finding pasted in place of `<PASTE_ONE_STAGE_1_FINDING_HERE>`. Save the result as `results/<name>/prompts/<SLUG>-NNN.md`. Start one subagent per finding and tell it to read its file and follow it. Run up to <N> subagents at a time.
5. When all subagents are done, write `results/<name>/index.md` with one line per finding: its ID, title, PQ classification, and prerequisites, linked to its report.
```
