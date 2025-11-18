[22/10 23:59] Davi Calixto: criar blockchain do zero Main net nome Calixto super token nativo Calixto fornecimento 10000000 valor 10 dólares endereço 0xD48915f5ba4D5a9A3013f9953bfab9C3354b4D59 class Blockchain: def __init__(self): self.chain = [self.create_genesis_block()] def create_genesis_block(self): return Block(0, "0", "Genesis Block") def add_block(self, data): previous_block = self.chain[-1] new_block = Block(len(self.chain), previous_block.hash, data) self.chain.append(new_block) # Exemplo de uso: blockchain = Blockchain() blockchain.add_block("Transação 1") blockchain.add_block("Transação 2") for block in blockchain.chain: print(f"Índice: {block.index}, Hash: {bloco.hash}") lançar main net.
[24/10 20:33] Davi Calixto: import hashlib
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
    difficulty = 4  # number of zeros required for proof of work

    def __init__(self):
        self.chain = []
        self.pending_transactions = []
        self.create_genesis_block()

    def create_genesis_block(self):
        genesis_block = Block(0, "0", time.time(), "Genesis Block", 0)
        self.chain.append(genesis_block)

    def get_last_block(self):
        return self.chain[-1]

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

# Uso inicial:
blockchain = Blockchain()
blockchain.add_block({"token": "Calixto", "action": "deploy", "supply": 10000000})
blockchain.add_block({"from": "0xD48915f5ba4D5a9A3013f9953bfab9c3354b4D59", "to": "address2", "amount": 1000})

for block in blockchain.chain:
    print(f"Index: {block.index}, Hash: {block.hash}")
[27/10 03:59] Davi Calixto: <!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calixto Token - Mineração</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #4527a0;
            --secondary-color: #7e57c2;
            --accent-color: #ff4081;
            --dark-color: #311b92;
            --light-color: #f5f5f5;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f8f9fa;
        }

        .navbar {
            background-color: var(--primary-color);
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .hero-section {
            background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
            color: white;
            padding: 80px 0;
            margin-bottom: 30px;
        }

        .card {
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0,0,0,0.08);
            transition: transform 0.3s ease;
            margin-bottom: 20px;
        }

        .card:hover {
            transform: translateY(-5px);
        }

        .token-display {
            margin-top: 20px;
        }

        .token-circle {
            width: 200px;
            height: 200px;
            background-color: rgba(255, 255, 255, 0.2);
            border-radius: 50%;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 72px;
            font-weight: bold;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
            border: 5px solid rgba(255, 255, 255, 0.3);
        }

        .mining-btn {
            position: relative;
            overflow: hidden;
            transition: all 0.3s ease;
        }

        .mining-btn:active {
            transform: scale(0.95);
        }

        .wallet-badge {
            background-color: rgba(255,255,255,0.2);
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 14px;
            display: flex;
            align-items: center;
        }

        .status-indicator {
            font-size: 8px;
            margin-right: 8px;
            color: #4CAF50;
        }

        #loadingOverlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.7);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            visibility: hidden;
            opacity: 0;
            transition: all 0.3s;
        }

        #loadingOverlay.active {
            visibility: visible;
            opacity: 1;
        }

        .spinner {
            width: 50px;
            height: 50px;
            border: 5px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
            margin-bottom: 20px;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .mining-status {
            background-color: rgba(0, 0, 0, 0.05);
            border-radius: 10px;
            padding: 15px;
        }

        .progress {
            height: 10px;
            border-radius: 5px;
        }

        .stats-card {
            text-align: center;
            padding: 20px;
        }

        .card-icon {
            font-size: 3rem;
            margin-bottom: 15px;
            color: var(--primary-color);
        }

        .section-header {
            margin-bottom: 40px;
        }

        .section-header h2 {
            color: var(--dark-color);
            margin-bottom: 10px;
        }

        .toast-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            z-index: 1000;
        }

        .toast {
            background-color: var(--dark-color);
            color: white;
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 10px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            display: flex;
            align-items: center;
            animation: slideIn 0.3s, fadeOut 0.5s 2.5s forwards;
        }

        .toast i {
            margin-right: 10px;
            font-size: 1.2rem;
        }

        @keyframes slideIn {
            from { transform: translateX(100%); }
            to { transform: translateX(0); }
        }

        @keyframes fadeOut {
            from { opacity: 1; }
            to { opacity: 0; }
        }

        .history-table {
            width: 100%;
            border-collapse: collapse;
        }

        .history-table th, .history-table td {
            padding: 12px 15px;
            text-align: left;
        }

        .history-table thead tr {
            background-color: var(--primary-color);
            color: white;
        }

        .history-table tbody tr:nth-child(even) {
            background-color: rgba(0,0,0,0.02);
        }

        .history-table tbody tr:hover {
            background-color: rgba(0,0,0,0.05);
        }

        @media (max-width: 768px) {
            .hero-section {
                padding: 50px 0;
            }
            
            .token-circle {
                width: 150px;
                height: 150px;
                font-size: 54px;
            }
            
            .d-flex.gap-3 {
                flex-direction: column;
                gap: 10px !important;
            }
            
            .d-flex.gap-3 .btn {
                width: 100%;
                margin-bottom: 10px;
            }
        }

        .loading-text {
            color: white;
            margin-top: 15px;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <!-- Loading Overlay -->
    <div id="loadingOverlay">
        <div class="spinner"></div>
        <p class="loading-text">Processando mineração...</p>
    </div>

    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark">
        <div class="container">
            <a class="navbar-brand" href="#">
                <i class="fas fa-coins me-2"></i> Calixto Token
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav me-auto">
                    <li class="nav-item">
                        <a class="nav-link active" href="#dashboard">Dashboard</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#mining">Mineração</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#history">Histórico</a>
                    </li>
                </ul>
                <div class="d-flex align-items-center">
                    <div id="walletBadge" class="wallet-badge me-2 d-none">
                        <i class="fas fa-circle status-indicator"></i>
                        <span id="shortAddress">-</span>
                    </div>
                    <button id="connectWalletBtn" class="btn btn-light btn-sm">
                        <i class="fas fa-wallet me-1"></i> Conectar Carteira
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <div class="hero-section">
        <div class="container">
            <div class="row align-items-center">
                <div class="col-lg-6">
                    <h1>Calixto Token</h1>
                    <p class="lead">Mine até 1000 blocos por dia!</p>
                    <p>Taxa de apenas 0.03% - A mais baixa do mercado!</p>
                    <div class="d-flex gap-3 mt-4">
                        <button id="installAppBtn" class="btn btn-light btn-lg">
                            <i class="fas fa-download me-2"></i> Instalar App
                        </button>
                        <a href="#mining" class="btn btn-primary btn-lg">
                            <i class="fas fa-hammer me-2"></i> Começar a Minerar
                        </a>
                    </div>
                </div>
                <div class="col-lg-6 text-center">
                    <div class="token-display">
                        <div class="token-circle">CLX</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Dashboard Section -->
    <section id="dashboard" class="py-5">
        <div class="container">
            <div class="section-header text-center mb-5">
                <h2>Seu Dashboard</h2>
                <p>Acompanhe seu progresso e ganhos</p>
            </div>
            
            <div class="row">
                <div class="col-md-4 mb-4">
                    <div class="card stats-card">
                        <div class="card-body">
                            <div class="card-icon">
                                <i class="fas fa-coins"></i>
                            </div>
                            <h5>Seu Saldo</h5>
                            <h3 id="userTokenBalance">0 CLX</h3>
                            <p class="text-muted" id="tokenValueUsd">≈ $0.00</p>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-4 mb-4">
                    <div class="card stats-card">
                        <div class="card-body">
                            <div class="card-icon">
                                <i class="fas fa-hammer"></i>
                            </div>
                            <h5>Blocos Minerados Hoje</h5>
                            <h3 id="blocksMinedToday">0 / 1000</h3>
                            <div class="progress mt-2">
                                <div class="progress-bar bg-success" id="miningProgress" role="progressbar" style="width: 0%"></div>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="col-md-4 mb-4">
                    <div class="card stats-card">
                        <div class="card-body">
                            <div class="card-icon">
                                <i class="fas fa-clock"></i>
                            </div>
                            <h5>Próximo Reset Diário</h5>
                            <h3 id="dailyResetTime">--:--:--</h3>
                            <p class="text-muted">Novos 1000 blocos disponíveis</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Mining Section -->
    <section id="mining" class="py-5 bg-light">
        <div class="container">
            <div class="section-header text-center mb-5">
                <h2>Mineração de Tokens</h2>
                <p>Mine até 1000 blocos por dia e ganhe 10 CLX por bloco</p>
            </div>
            
            <div class="row justify-content-center">
                <div class="col-lg-8">
                    <div class="card">
                        <div class="card-body">
                            <div class="mining-status mb-4">
                                <div class="d-flex justify-content-between mb-2">
                                    <span>Status:</span>
                                    <span id="miningStatus">Aguardando conexão da carteira</span>
                                </div>
                                <div class="d-flex justify-content-between mb-2">
                                    <span>Próxima mineração:</span>
                                    <span id="nextMiningTime">--:--:--</span>
                                </div>
                                <div class="d-flex justify-content-between mb-2">
                                    <span>Blocos restantes hoje:</span>
