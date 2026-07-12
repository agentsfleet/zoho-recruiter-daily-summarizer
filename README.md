# zoho-recruiter-daily-summarizer

A prebuilt `agentsfleet` fleet that reads the day's movement in a Zoho Recruit hiring pipeline and posts one short digest — who advanced, who is waiting on us, which roles are going cold. Read-only against Zoho Recruit: it reports, it never advances, rejects, or emails anyone.

By default it wakes each weekday morning on a cron schedule; you can also ask it directly with `agentsfleet steer`.

## The bundle

| File | Role |
|---|---|
| `SKILL.md` | The fleet's instructions — which endpoints it calls, how it groups the pipeline, what it must never disclose. |
| `TRIGGER.md` | The cron wake rule, tool allowlist, credential names, network allowlist, and spend caps. |

The frontmatter `name:` in both files is `zoho-recruiter-daily-summarizer`, matching this repository. That identity is load-bearing: the importer rejects a bundle whose `SKILL.md` and `TRIGGER.md` names disagree, and the fleet-library catalog id is taken from that name.

## Candidate privacy

The digest names candidates and their pipeline stage. It never quotes résumé contents, salary expectations, personal contact details, or interviewer notes — those stay in Zoho Recruit behind its own access controls. The constraint is written into `SKILL.md`, and the fleet refuses prompts that ask it to include them.

## Install

```bash
agentsfleet secret create zoho_recruit --data='{
  "client_id":"<zoho client id>",
  "client_secret":"<zoho client secret>",
  "refresh_token":"<zoho refresh token>"
}'

agentsfleet install --library zoho-recruiter-daily-summarizer
agentsfleet steer <FLEET_ID> "summarize the pipeline"
```

The fleet refreshes its own short-lived access token against `accounts.zoho.com` at the start of each run. Credentials resolve from your vault at the HTTPS boundary, after the sandbox has closed around the agent — the fleet only ever sees `${secrets.zoho_recruit.refresh_token}`-style placeholders, never the raw bytes.

## Spend

`$0.50` daily and `$5.00` monthly, declared in `TRIGGER.md`. The first cap to trip blocks further runs. One digest costs a few cents.
