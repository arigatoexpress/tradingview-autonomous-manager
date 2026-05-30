# TradingView Autonomous Manager — Agent Notes

Read this first before editing code, docs, or configuration.

## What this repo does

Autonomous TradingView Desktop control and Pine Script strategy management that forwards signals to Sapphire for paper or live execution. Built for Raspberry Pi deployment with a FastAPI backend.

## Key directories and files

| Path | Purpose |
|---|---|
| `backend/main.py` | FastAPI app entrypoint |
| `backend/app/core/` | TradingView Desktop controller + config loader |
| `backend/app/strategies/` | Strategy manager |
| `backend/app/watchlist/` | Watchlist manager |
| `backend/app/services/` | Community script tester |
| `backend/app/integrations/` | Sapphire bridge |
| `config/settings.yaml` | Runtime configuration |
| `deploy_to_pi.py` | Pi mesh deployment script |
| `pine_scripts/` | Pine Script strategies |
| `webhook_server.py` | Standalone webhook receiver |

## How to run the dev server

```bash
cd backend
source venv/bin/activate
uvicorn main:app --host 0.0.0.0 --port 8081
```

## Tests

There is no formal test suite yet. Validate by running:

```bash
python test_api.py
python test_system.py
```

## Safety boundaries

- **Never** hardcode webhook secrets or API keys in source.
- **Never** deploy to live Pi nodes without explicit confirmation.
- **Never** delete `config/settings.yaml` or `pine_scripts/` without backup.
- Do not change the Sapphire bridge signal format without coordinating with `~/Code/Sapphire`.
