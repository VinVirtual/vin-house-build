# vin-house-build

[![ci](https://github.com/VinVirtual/vin-house-build/actions/workflows/ci.yml/badge.svg)](https://github.com/VinVirtual/vin-house-build/actions/workflows/ci.yml) [![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

The house flow for building and shipping a skill, tool or small project with agents, from brief to release.

Every step leaves evidence (a test run, a screenshot, a report) rather than a claim: a brief, a
build agent in its own folder, a real end-to-end run, a fresh-clone test, an independent review,
fixes, a privacy scan, and publishing only after the owner says yes. It comes with the rules to
put in every agent brief and two scripts.

It is a [Claude Code](https://claude.com/claude-code) skill: `SKILL.md` gives Claude the flow and
the agent rules when you say "ship this" or start agents on a build.

## Install

As a Claude Code plugin from the [VinSkills](https://github.com/VinVirtual/vinskills) marketplace:

```bash
claude plugin marketplace add VinVirtual/vinskills
```

```
/plugin install vin-house-build@vinskills        # this skill alone
/plugin install vin-core@vinskills         # or all eight vin-core skills
```

Or as a plain skill folder:

```bash
git clone https://github.com/VinVirtual/vin-house-build ~/.claude/skills/vin-house-build
~/.claude/skills/vin-house-build/check_env.sh
```

Needs Node.js 22 or newer and git. The publishing step uses the `gh` CLI.

## The two scripts

```bash
node scripts/privacy-scan.mjs path/to/repo --private-words words.txt
scripts/fresh-clone-check.sh path/to/repo            # clones the last commit into .scratch/ and runs the tests
```

- **privacy-scan.mjs** looks at what git would publish (tracked plus untracked, not ignored) for
  e-mail addresses (GitHub no-reply allowed), key-shaped tokens (GitHub, Anthropic, OpenAI, AWS,
  Google, Slack, Stripe, Hugging Face, JWTs, private keys, passwords in URLs), home-folder paths
  that name a user, and any words you list. It never prints a secret's value. `--json` for
  machines; exit code 1 when anything is found.
- **fresh-clone-check.sh** clones the last commit into a throwaway folder inside the repo's
  `.scratch/` and runs `npm test`, else `pytest`, else `./check_env.sh`, or the command you give.
  It catches files that exist on your disk but were never committed.

## The flow

1. Brief: goal, folder, what "done" means, the rules, the report format ([template](references/brief-template.md)).
2. One build agent per item, in its own folder, matching two existing repos of the same kind.
3. Run the pipeline end to end and look at the outputs. No invented numbers.
4. Fresh-clone check.
5. Independent review by an agent that gets the code and the brief, not the builder's summary.
6. Fix every critical and high finding; review again if the first round found anything serious.
7. Privacy scan, zero errors.
8. Ask the owner. Publish only after an explicit yes.
9. Create the repo, push, add topics, wait for green CI, release with notes from the real commits.

Details and commands: [references/flow.md](references/flow.md). The agent rules and the reasons
behind them: [references/agent-rules.md](references/agent-rules.md).

## Tests

```bash
npm test
```

The suite runs offline with Node's built-in test runner. CI runs it on every push, together
with [skill-ci](https://github.com/VinVirtual/skill-ci), which lints the SKILL.md, README,
licence, secrets and scripts.

## Part of VinSkills

vin-house-build is one of the eight `vin-core` skills in [VinSkills](https://github.com/VinVirtual/vinskills),
my Claude Code marketplace: [vin-house-build](https://github.com/VinVirtual/vin-house-build),
[vin-agent-orchestration](https://github.com/VinVirtual/vin-agent-orchestration),
[vin-site-qa](https://github.com/VinVirtual/vin-site-qa), [vin-meter](https://github.com/VinVirtual/vin-meter),
[vin-navigator](https://github.com/VinVirtual/vin-navigator), [vin-docs](https://github.com/VinVirtual/vin-docs),
[vin-squeeze](https://github.com/VinVirtual/vin-squeeze) and [vin-scriptlens](https://github.com/VinVirtual/vin-scriptlens).

## Licence

MIT, see [LICENSE](LICENSE).
