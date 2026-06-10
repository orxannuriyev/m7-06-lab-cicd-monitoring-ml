#Rollback Runbook

## When to Roll Back

Roll back immediately if any of the following conditions persist for 5 minutes after deployment:

- Availability error rate > 1%
- P99 latency > 1 second
- AvailabilityBurnFast alert is firing
- ModelVersionMismatch alert is firing
- Quality proxy degradation > 5% (e.g., moderation precision or acceptance rate)

## How to Roll Back

If the deployment is in canary or production:

```bash
./scripts/rollback.sh production
```

Or trigger the GitHub Actions rollback workflow:

1. Go to **Actions**
2. Select **Rollback**
3. Click **Run workflow**
4. Choose:
   - Environment: `production`
   - Target Version: previous stable SHA

Wait for rollback completion before taking any further action.

## What to Verify

Confirm all of the following are green:

- AvailabilityBurnFast = OK
- LatencyP99High = OK
- ModelVersionMismatch = OK
- Error rate < 0.5%
- P99 latency < 1 second
- Production Health Dashboard = Green
- Canary Verification Dashboard = Green

## Who to Notify

Notify:

- PagerDuty incident channel
- #vision-moderation-prod Slack channel
- On-call SRE
- ML Service Owner

Include:

- Incident ID
- Rollback timestamp
- Affected version SHA

## What Not to Do

- Do not roll forward before identifying the root cause.
- Do not disable alerts to clear dashboards.
- Do not deploy additional changes during the incident.
- Do not assume rollback succeeded without verification.

## When to Roll Forward

Re-deploy only when:

- Root cause is identified and documented.
- Fix is reviewed and merged.
- Unit tests pass.
- Contract tests pass.
- Smoke tests pass.
- Staging validation succeeds.
- Canary runs successfully for at least 30 minutes with no critical alerts.