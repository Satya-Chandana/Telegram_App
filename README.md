# Secure Multi-User Messaging System Using Lightweight MTProto Encryption

This project implements a secure, browser-based multi-user messaging system inspired by Telegram's MTProto protocol. The application emphasizes client-side encryption, session management, and secure peer-to-peer communication using cryptographic primitives such as ECDH, AES-CBC, and SHA-256.

## Features

- Secure key exchange using ECDH and Diffie-Hellman principles
- AES-CBC encryption with custom key and IV derivation
- Client-side encryption and decryption; the server never sees plain text
- Lightweight Flask backend to handle message relay and metadata management
- Real-time multi-user chat interface with peer discovery

## Technologies Used

- Flask (Python backend)
- Crypto libraries: PyCryptodome (backend), Web Crypto API + CryptoJS (frontend)
- JavaScript (client-side cryptography and messaging logic)
- HTML/CSS (modern responsive chat interfaces)

## File Overview

### Backend

- `server.py`: Core backend logic including secure session setup, message routing, ECDH key handling, and Flask routes.
- `requirements.txt`: Python dependencies.

### Frontend

- `client_ui.html`: Material-style interface with secure session handling, peer selection, and encrypted messaging.
- `chat_ui_multiuser.html`: Lightweight chat UI for testing handshake and encrypted exchange logic.

## How It Works

1. **Handshake Phase**:
   - The client performs a Diffie-Hellman handshake with the server.
   - A session ID and an authorization key are generated and stored locally.

2. **ECDH Setup**:
   - Clients generate ECDH key pairs and share public keys.
   - A shared secret key is derived for each peer pair.

3. **Message Encryption**:
   - AES-CBC is used for encryption, with custom key/IV derivation.
   - Messages are padded and encrypted client-side.

4. **Message Exchange**:
   - Encrypted messages are sent to `/secure_message`.
   - Clients poll `/poll_messages` to fetch and decrypt new messages.


### Installation
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Running the Server
```bash
python server.py
```
Server will start on: http://localhost:8080

## API Endpoints

### 1. POST `/req_pq`

Request:
```json
{
  "nonce": "random_nonce_base64"
}
Response:
{
  "nonce": "...",
  "server_nonce": "...",
  "pq": "hex_pq",
  "fingerprint": "hex_fingerprint"
}

### 2. POST '/set_client_dh_params'
Request:
{
  "nonce": "...",
  "server_nonce": "...",
  "g_b": "hex_dh_value",
  "client_id": "user123"
}
Response:
{
  "auth_key": "base64_auth_key",
  "session_id": "base64_session_id",
  "salt": "base64_salt",
  "status": "Auth key established"
}

### 3. POST '/secure_message'
Request:
{
  "client_id": "user123",
  "target_peer": "user456",
  "message_id": "64bit_message_id",
  "seq_no": 0,
  "encrypted_data": [72, 101, 108, 108, 111],
  "message_key": [10, 20, 30, 40]
}
Response:
{
  "msg_id": "64bit_message_id",
  "seq_no": 1,
  "status": "Message received securely",
  "timestamp": "ISO_8601_timestamp",
  "last_msg_id": "64bit_message_id"
}


