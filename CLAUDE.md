# CLAUDE.md – velomate_test

A test copy of VeloMate (`elduty/velomate`, AGPL-3.0): a self-hosted cycling data platform that
ingests rides from Strava into PostgreSQL, shows them in Grafana, and plans routes. There is no
upstream remote; `origin` is `andersbjorkh/velomate_test`. The only local change so far raises
the Strava backfill from 12 to 24 months (`ingestor/strava.py`).

## Commands

```bash
pip install --break-system-packages -r requirements.txt pytest
pytest
cp .env.example .env && cp config.example.yaml config.yaml
docker compose up -d        # ingestor, PostgreSQL, Grafana
```

Containers in this LXC need `security_opt: [apparmor=unconfined]`, and `docker build` is blocked
here by AppArmor, so the ingestor image may not build locally.

## Layout

- `ingestor/` – Strava polling (every 10 minutes), per-second streams, and fitness metrics
  (CTL/ATL/TSB, TRIMP, NP, IF, EF, FTP estimate). Its own Dockerfile and requirements.
- `velomate/` – the CLI and route planner (geocoding, weather, route generation).
- `grafana/` – dashboards and provisioning. `tests/` – pytest suite.

## Conventions

- Cycling only: Ride, VirtualRide and EBikeRide are ingested; everything else is filtered out.
- Derived metrics are computed by the ingestor and stored; Grafana reads stored values only.
- Never commit `.env` or `config.yaml` (Strava credentials).
