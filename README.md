# Chain
criar blockchain do zero Main net nome Calixto super token nativo Calixto fornecimento 10000000 valor 10 dólares endereço 0xD48915f5ba4D5a9A3013f9953bfab9C3354b4D59 class Blockchain: def __init__(self): self.chain = [self.create_genesis_block()] def create_genesis_block(self): return Block(0, "0", "Genesis Block") def add_block(self)
