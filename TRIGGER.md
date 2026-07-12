---
name: zoho-recruiter-daily-summarizer
x-agentsfleet:
  triggers:
    - type: cron
      # Weekday mornings, so the digest lands before the first screen of the
      # day. The operator can override the expression at install time.
      schedule: "0 9 * * 1-5"

  tools:
    - http_request
    - memory_recall
    - memory_store

  credentials:
    - zoho_recruit
    # Credential shape:
    #   zoho_recruit = { client_id, client_secret, refresh_token }
    # Substituted into http_request at the tool bridge as
    # ${secrets.zoho_recruit.FIELD} after the runner sandbox has closed around
    # the agent — the fleet never sees raw bytes, so the worst a prompt
    # injection can extract is the placeholder string.

  network:
    allow:
      - recruit.zoho.com
      - accounts.zoho.com

  budget:
    daily_dollars: 0.50
    monthly_dollars: 5.00
---
# Wake rule

Wakes on a schedule — by default each weekday at 09:00 — and summarizes the
hiring pipeline's movement since the previous digest. Also reachable on demand
with `agentsfleet steer <FLEET_ID> "summarize the pipeline"`.
