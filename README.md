# Blockchain-based-Identity-Verification
Use blockchain technology to create a secure and decentralized identity verification system for various applications, such as online banking or voting.
import hashlib
import json
from time import time
from uuid import uuid4

class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_transactions = []
        self.nodes = set()
        self.new_block(previous_hash='1', proof=100)

    def register_node(self, address):
        self.nodes.add(address)

    def new_block(self, proof, previous_hash=None):
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'transactions': self.current_transactions,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }
        self.current_transactions = []
        self.chain.append(block)
        return block

    def new_transaction(self, sender, recipient, identity_data):
        self.current_transactions.append({
            'sender': sender,
            'recipient': recipient,
            'identity_data': identity_data,
        })
        return self.last_block['index'] + 1

    @staticmethod
    def hash(block):
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    @property
    def last_block(self):
        return self.chain[-1]

    def proof_of_work(self, last_proof):
        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1
        return proof

    @staticmethod
    def valid_proof(last_proof, proof):
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[:4] == "0000"

blockchain = Blockchain()
sender = str(uuid4()).replace('-', '')
recipient = 'onlineBankingPlatform'
identity_data = json.dumps({'name': 'John Doe', 'id': '1234567890', 'verified': True})
blockchain.new_transaction(sender=sender, recipient=recipient, identity_data=identity_data)
last_proof = blockchain.last_block['proof']
proof = blockchain.proof_of_work(last_proof)
previous_hash = blockchain.hash(blockchain.last_block)
block = blockchain.new_block(proof, previous_hash)

print(json.dumps(block, indent=4))
