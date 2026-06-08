# 🏊 Calixto Token - Blockchain Mining Pool

A complete self-hosted Bitcoin Mainnet mining pool with real consensus validation, Stratum V1 server, and web-based dashboard.

## 🌟 Features

✅ **Self-Custody** — Full control over your blockchain, pool, and payouts  
✅ **Real Bitcoin Core 27.0** — Complete consensus validation with full node  
✅ **Stratum V1 Server** — Support for ASIC miners and CPU miners  
✅ **Share Validation** — Server-side SHA-256d validation for every share  
✅ **Block Submission** — Real blocks submitted to Mainnet via `submitblock`  
✅ **Honest Hashrate** — No fake metrics; real-time worker monitoring  
✅ **Web Dashboard** — Next.js frontend with live workers, shares, and statistics  
✅ **Docker Ready** — Production-grade containerized deployment  

---

## 📋 Table of Contents

- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [API Reference](#api-reference)
- [Monitoring](#monitoring)
- [Troubleshooting](#troubleshooting)

---

## 📖 Overview

**Calixto Token (Phase 2 Mining Pool)** is a complete self-hosted mining pool infrastructure:

- **Blockchain**: Custom blockchain implementation with Proof-of-Work consensus
- **Mining Pool**: Stratum V1 compatible server accepting miner connections
- **Dashboard**: Real-time monitoring and statistics
- **API**: RESTful backend with WebSocket support for live updates

### Token Specifications

| Property | Value |
|----------|-------|
| **Name** | Calixto Token |
| **Symbol** | CLX |
| **Supply** | 10,000,000 |
| **Price** | $10 USD (initial) |
| **Mining Reward** | 10 CLX per block |
| **Daily Limit** | 1,000 blocks/day per address |
| **Pool Fee** | 0.03% |

---

## 🏗️ System Architecture

### Data Flow

```
Bitcoin Core (Full Node)
    ↓ [getblocktemplate + ZMQ]
    ↓
Stratum Server
    ↓ [mining.notify]
    ↓
Miner Client (ASIC/CPU)
    ↓ [Share Submission]
    ↓
Pool Validator
    ├→ SHA-256d validation
    ├→ Share target check
    └→ Network target check
    ↓
Block Found?
    ├→ Yes: submitblock to bitcoind
    └→ No: Store in statistics
    ↓
Backend API
    ↓ [WebSocket Stream]
    ↓
Frontend Dashboard
```

### Components

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Bitcoin Core** | C++ | Full node consensus validation |
| **Stratum Server** | Node.js | Miner connection & job distribution |
| **Pool Backend** | Node.js | Stats API & WebSocket stream |
| **Frontend** | Next.js | Real-time dashboard UI |
| **Database** | SQLite/PostgreSQL | Worker stats & block history |

---

## ⚙️ Prerequisites

### Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | 4 cores | 8+ cores |
| **RAM** | 8 GB | 16+ GB |
| **Disk** | 800 GB | 1-2 TB SSD |
| **Network** | 100 Mbps | 1 Gbps |

### Software Requirements

- **Docker** 20.10+
- **Docker Compose** 2.0+
- **Node.js** 20+ (for local development)
- **Git**

### Mainnet Requirements

⚠️ **Critical**: Before deployment, you must have:

- A **Bitcoin address you control** (for `POOL_PAYOUT_ADDRESS`)
  - Hardware wallet (Ledger, Trezor)
  - Exchange withdrawal address
  - Self-hosted wallet
- **DO NOT use an address belonging to someone else**

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/davilibanio3-alt/Opus-Davi.git
cd Opus-Davi
npm install
```

### 2. Build Workspaces

```bash
npm run build -w tx-engine
npm run build -w recovery
npm run build -w mining
npm run build -w ai-engine
```

### 3. Configure Environment

Create `.env.local`:

```env
# Backend
PORT=8787
HOST=0.0.0.0
NODE_ENV=production
CORS_ORIGINS=http://localhost:3000

# Bitcoin Core RPC
BITCOIN_RPC_URL=http://bitcoind:18332
BITCOIN_RPC_USER=bitcoind
BITCOIN_RPC_PASSWORD=your-secure-password-here

# Pool Configuration
POOL_PAYOUT_ADDRESS=bc1q...  # Your Mainnet address (REQUIRED)
POOL_ENABLE_CPU_MINER=false

# Frontend
NEXT_PUBLIC_BACKEND_URL=http://localhost:8787
NEXT_PUBLIC_MEMPOOL_API=https://mempool.space/api
NEXT_PUBLIC_MEMPOOL_WS=wss://mempool.space/api/v1/ws
NEXT_PUBLIC_NETWORK=mainnet
```

### 4. Start Services

```bash
# Start all services (Bitcoin Core + Pool + Dashboard)
docker-compose up -d --build

# Start only the miner (for testing)
docker-compose up -d --build miner
```

### 5. Access Dashboard

- **Dashboard**: http://localhost:3000
- **Backend API**: http://localhost:8787
- **Bitcoin RPC**: http://localhost:18332

---

## 🔧 Configuration

### Bitcoin Core Sync (IBD - Initial Block Download)

For Mainnet, initial block download takes:
- **Time**: 1-4 days
- **Bandwidth**: ~200 GB
- **Disk**: ~655 GB (unpruned) or ~15 GB (pruned=10000)

Monitor sync progress:

```bash
docker exec bitcoind bitcoin-cli getblockchaininfo
```

### Pool Configuration

| Variable | Purpose | Default |
|----------|---------|---------|
| `POOL_PAYOUT_ADDRESS` | Where mined blocks are paid | **REQUIRED** |
| `POOL_ENABLE_CPU_MINER` | Enable CPU miner sidecar | `false` |
| `STRATUM_PORT` | Stratum server port | `3333` |
| `STRATUM_DIFFICULTY` | Initial share difficulty | `1` |
| `POOL_FEE_PERCENT` | Pool fee (%) | `0.03` |

---

## 📊 API Reference

### Pool Statistics

```bash
GET /api/pool/stats
```

Response:
```json
{
  "height": 952785,
  "difficulty": 92846230150,
  "networkHashrate": "123456789 GH/s",
  "activeWorkers": 42,
  "sharesPerSecond": 156789,
  "blocksFound": 512
}
```

### Worker Statistics

```bash
GET /api/workers/:address
```

Response:
```json
{
  "address": "0xD48915f5ba4D5a9A3013f9953bfab9C3354b4D59",
  "balance": 100000,
  "shares": 5000,
  "rejectedShares": 12,
  "hashrate": "45 GH/s",
  "lastSeen": "2026-06-08T14:00:00Z"
}
```

### WebSocket Events

Connect to `ws://localhost:8787/api/pool/stream` for live updates:

```javascript
// Worker joined
{ "type": "worker_joined", "address": "0x...", "hashrate": "10 GH/s" }

// New share
{ "type": "share", "worker": "0x...", "difficulty": 1024 }

// Block found
{ "type": "block", "height": 952786, "hash": "0x..." }
```

---

## 📡 Monitoring

### Health Check

```bash
curl http://localhost:8787/api/health
```

### View Pool Logs

```bash
docker logs -f opus-davi-pool
```

### View Bitcoin Core Logs

```bash
docker logs -f bitcoind
```

### Monitor Worker Performance

```bash
curl http://localhost:8787/api/pool/workers | jq '.'
```

---

## 🐛 Troubleshooting

### Bitcoin Core Not Syncing

```bash
# Check blockchain info
docker exec bitcoind bitcoin-cli getblockchaininfo

# Check network connections
docker exec bitcoind bitcoin-cli getnetworkinfo

# Restart bitcoind
docker restart bitcoind
```

### Pool Server Not Starting

**Error**: `Pool refuses to start`
- **Cause**: `POOL_PAYOUT_ADDRESS` not set
- **Solution**: Set a valid Bitcoin address in `.env.local`

### Miners Disconnecting

**Error**: `Connection timeout`
- **Cause**: Firewall blocking port 3333
- **Solution**: Open port 3333 (TCP) in firewall

```bash
# Linux (ufw)
sudo ufw allow 3333/tcp

# macOS (pfctl)
echo "pass in proto tcp from any to any port 3333" | sudo pfctl -ef -
```

### Low Hashrate

- Ensure enough CPU/GPU resources
- Check network latency (>500ms is problematic)
- Verify miner configuration

---

## 📝 Python Blockchain Implementation

The core blockchain is implemented in Python:

```python
import hashlib
import time
import json

class Block:
    def __init__(self, index, previous_hash, timestamp, data, proof):
        self.index = index
        self.previous_hash = previous_hash
        self.timestamp = timestamp
        self.data = data
        self.proof = proof
        self.hash = self.compute_hash()

    def compute_hash(self):
        block_string = json.dumps({
            'index': self.index,
            'previous_hash': self.previous_hash,
            'timestamp': self.timestamp,
            'data': self.data,
            'proof': self.proof
        }, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

class Blockchain:
    difficulty = 4

    def __init__(self):
        self.chain = []
        self.pending_transactions = []
        self.create_genesis_block()

    def create_genesis_block(self):
        genesis_block = Block(0, "0", time.time(), "Genesis Block", 0)
        self.chain.append(genesis_block)

    def proof_of_work(self, last_proof):
        proof = 0
        while not self.valid_proof(last_proof, proof):
            proof += 1
        return proof

    def valid_proof(self, last_proof, proof):
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[:self.difficulty] == '0' * self.difficulty

    def add_block(self, data):
        last_block = self.get_last_block()
        last_proof = last_block.proof
        proof = self.proof_of_work(last_proof)
        block = Block(index=len(self.chain),
                      previous_hash=last_block.hash,
                      timestamp=time.time(),
                      data=data,
                      proof=proof)
        self.chain.append(block)
```

**Usage**:

```python
blockchain = Blockchain()
blockchain.add_block({"token": "Calixto", "action": "deploy", "supply": 10000000})
blockchain.add_block({"from": "0xD48915f5ba4D5a9A3013f9953bfab9c3354b4D59", "to": "address2", "amount": 1000})

for block in blockchain.chain:
    print(f"Index: {block.index}, Hash: {block.hash}")
```

---

## 📚 Development

### Local Testing

```bash
# Start development environment
npm run dev

# Run tests
npm test

# Build for production
npm run build
```

### File Structure

```
.
├── docker-compose.yml      # Container orchestration
├── .env.local              # Environment configuration
├── packages/
│   ├── tx-engine/          # Transaction processing
│   ├── recovery/           # Block recovery & validation
│   ├── mining/             # Stratum server & pool logic
│   └── ai-engine/          # AI-powered analytics
├── frontend/               # Next.js dashboard
└── docs/                   # Documentation
```

---

## 🔐 Security Notes

⚠️ **Important**:

1. **Private Keys**: Never commit `.env` files with real private keys
2. **RPC Password**: Use a strong, random password for Bitcoin Core RPC
3. **Firewall**: Restrict Stratum port (3333) to trusted miners only
4. **HTTPS**: Use HTTPS for production dashboard deployments
5. **Rate Limiting**: Implement rate limiting on API endpoints

---

## 📄 License

This project is licensed under the MIT License. See LICENSE file for details.

---

## 👤 Author

**Davi Calixto** (@davilibanio3-alt)

---

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/davilibanio3-alt/Opus-Davi/issues)
- **Documentation**: [GitHub Wiki](https://github.com/davilibanio3-alt/Opus-Davi/wiki)
- **Community**: Discussions in GitHub

---

## 🔄 Version History

- **Phase 2** (Current) - Full mining pool with Stratum V1
- **Phase 1** - Basic blockchain implementation

---

**Last Updated**: June 8, 2026  
**Status**: Active Development ✅
