# TradingView Desktop Autonomous Manager

Autonomous management system for TradingView Desktop that integrates with your existing Sapphire trading infrastructure on ASTER and LIGHTER DEXs.

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        TRADINGVIEW DESKTOP AUTONOMOUS MANAGER                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────────┐  │
│  │  TradingView    │    │   FastAPI       │    │    React Dashboard      │  │
│  │  Desktop App    │◄──►│   Backend       │◄──►│    (Web UI)             │  │
│  │                 │    │   (Port 8081)   │    │                         │  │
│  └─────────────────┘    └────────┬────────┘    └─────────────────────────┘  │
│           ▲                      │                                          │
│           │ Playwright           │                                          │
│           │ Automation           │                                          │
│           ▼                      ▼                                          │
│  ┌─────────────────┐    ┌─────────────────────────────────────────────┐     │
│  │  Pine Script    │    │  Services:                                   │     │
│  │  Strategies     │    │  • Strategy Manager                          │     │
│  │  Indicators     │    │  • Watchlist Manager                         │     │
│  │  Alerts         │    │  • Community Script Tester                   │     │
│  └─────────────────┘    │  • Signal Scanner                            │     │
│                         └──────────────────┬────────────────────────────┘     │
│                                            │                                 │
│                                            ▼                                 │
│                         ┌─────────────────────────────────────────────┐     │
│                         │  Sapphire Bridge                             │     │
│                         │  • Webhook Integration                       │     │
│                         │  • Signal Forwarding                         │     │
│                         │  • Venue Routing (ASTER/LIGHTER)             │     │
│                         └──────────────────┬────────────────────────────┘     │
│                                            │                                 │
└────────────────────────────────────────────┼─────────────────────────────────┘
                                             │
                    ┌────────────────────────┼────────────────────────┐
                    │                        │                        │
                    ▼                        ▼                        ▼
           ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
           │   rari1 (Pi)    │    │   rari2 (Pi)    │    │   GCloud        │
           │                 │    │                 │    │                 │
           │  Signal Proc    │    │  Trade Executor │    │  Sapphire       │
           │                 │    │  PnL Tracker    │    │  Alpha Engine   │
           └─────────────────┘    └─────────────────┘    └─────────────────┘
                    │                        │                        │
                    └────────────────────────┴────────────────────────┘
                                              │
                                              ▼
                                    ┌─────────────────┐
                                    │  ASTER DEX      │
                                    │  LIGHTER DEX    │
                                    └─────────────────┘
```

## Features

### TradingView Desktop Automation
- **Playwright-based control** of TradingView Desktop application
- **Symbol & Timeframe management** - Automated chart navigation
- **Indicator management** - Add/remove technical indicators
- **Strategy deployment** - Apply Pine Script strategies
- **Screenshot capture** - Visual chart monitoring
- **Watchlist sync** - Bidirectional watchlist management

### Strategy Management
- Pine Script storage and versioning
- Strategy validation (syntax checking)
- Backtest result tracking
- Strategy activation/deactivation
- Template generation

### Watchlist Management
- Multiple watchlist support
- Symbol categorization (ASTER/LIGHTER venues)
- Bulk import/export
- TradingView sync
- Default crypto/forex lists

### Community Script Tester
- Automated script testing
- Repaint/lookahead detection
- Performance benchmarking
- Visual quality assessment
- Approval workflow for Sapphire

### Sapphire Integration
- Webhook signal forwarding
- Venue routing (ASTER/LIGHTER)
- Heartbeat monitoring
- Status reporting
- TradingView webhook format compatibility

## Quick Start

### Prerequisites
- Python 3.11+
- Playwright: `pip install playwright && playwright install chromium`
- TradingView Desktop installed

### Installation

```bash
# Clone or copy to your Raspberry Pi
cd ~/tv-autonomous-manager/backend

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Start the server
uvicorn main:app --host 0.0.0.0 --port 8081
```

### Configuration

Edit `config/settings.yaml`:

```yaml
sapphire:
  webhook_url: "http://100.87.225.89:8080/tradingview/webhook"
  webhook_secret: "your-secret-here"

tradingview:
  headless: false  # Set true for headless operation
  cdp_port: 9222   # Chrome DevTools Protocol port
```

### Deployment to Pi Mesh

```bash
# Deploy to all Pis
python deploy_to_pi.py --host all

# Or deploy to specific host
python deploy_to_pi.py --host rari2
```

## API Endpoints

### TradingView Control
- `POST /tv/connect` - Connect to TradingView Desktop
- `GET /tv/state` - Get current chart state
- `POST /tv/symbol/{symbol}` - Change symbol
- `POST /tv/timeframe/{timeframe}` - Change timeframe
- `POST /tv/indicator/{name}` - Add indicator
- `POST /tv/screenshot` - Capture chart

### Strategies
- `GET /strategies` - List strategies
- `POST /strategies` - Create strategy
- `POST /strategies/{id}/apply` - Apply to chart
- `POST /strategies/{id}/activate` - Activate

### Watchlists
- `GET /watchlists` - List watchlists
- `POST /watchlists` - Create watchlist
- `POST /watchlists/{id}/sync` - Sync to TradingView
- `POST /watchlists/{id}/symbols/{symbol}` - Add symbol

### Community Scripts
- `GET /scripts` - List scripts
- `POST /scripts` - Import script
- `POST /scripts/{id}/test` - Test script
- `POST /scripts/{id}/approve` - Approve for Sapphire

### Signals
- `POST /signals/send` - Send signal to Sapphire
- `POST /signals/scan` - Scan for signals

### Sapphire Integration
- `GET /sapphire/status` - Get Sapphire status
- `POST /sapphire/heartbeat` - Send heartbeat

## WebSocket Real-time Updates

Connect to `ws://localhost:8081/ws` for real-time updates:
- Chart state changes
- Signal detections
- Test completion notifications

## TradingView Workbench Integration

This system complements the existing Sapphire TradingView workbench design:

1. **Research Phase**: Use TradingView Desktop to develop/test strategies
2. **Validation Phase**: Use Community Script Tester to validate quality
3. **Deployment Phase**: Apply approved strategies to live charts
4. **Monitoring Phase**: Continuous signal scanning and forwarding

## Directory Structure

```
TradingViewAutonomousManager/
├── backend/
│   ├── app/
│   │   ├── core/              # TV Desktop Controller
│   │   ├── api/               # API routes
│   │   ├── services/          # Script tester
│   │   ├── strategies/        # Strategy manager
│   │   ├── watchlist/         # Watchlist manager
│   │   └── integrations/      # Sapphire bridge
│   ├── main.py               # FastAPI app
│   └── requirements.txt
├── config/
│   └── settings.yaml
├── deploy_to_pi.py           # Deployment script
└── README.md
```

## Integration with Existing System

This manager connects to your existing Sapphire infrastructure:

- **Signals** → `sapphire-alpha` webhook endpoint
- **Venue routing** → ASTER (SOL ecosystem) or LIGHTER (multi-asset)
- **Monitoring** → Existing alert system on rari2
- **Execution** → Trade executor on Pi mesh

## Security Considerations

- Webhook secrets for authentication
- Tailscale mesh networking for secure Pi communication
- No TradingView credentials stored (uses Desktop app)
- Strategy validation before deployment

## Troubleshooting

### TradingView not connecting
1. Ensure TradingView Desktop is running
2. Check CDP port (9222) is accessible
3. Try launching TV with: `--remote-debugging-port=9222`

### Signals not reaching Sapphire
1. Check webhook URL in config
2. Verify webhook secret is set
3. Test with: `POST /sapphire/heartbeat`

### Playwright issues
1. Reinstall browsers: `playwright install chromium`
2. Check system dependencies

## License

Part of the Sapphire Trading System - Private use only.
