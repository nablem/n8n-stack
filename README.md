# n8n Docker Stack (Self-Hosted)

A production-ready Docker Compose setup for **n8n** using **Traefik** for automatic SSL (Let's Encrypt), **PostgreSQL** as the database, and **Redis** for queue management (scaling with workers).

## Quick Start

### 1. Server Preparation
Ensure you have Docker and Docker Compose installed. You will also need a domain name pointing to your server's IP.

```bash
# Create project folder
git clone https://github.com/nablem/n8n-stack.git
cd n8n-stack

# Create SSL storage file with correct permissions
mkdir letsencrypt
touch letsencrypt/acme.json
chmod 600 letsencrypt/acme.json
```

### 2. Configuration
Create a `.env` file in the root directory to store your credentials (do not share this file):

```bash
cp .env.example .env
nano .env
```

Edit the following:
```
# Timezone
TZ=Europe/Paris

# Domain and SSL
HOST=n8n.yourdomain.com
EMAIL_SSL=your-email@example.com

# Database
POSTGRES_USER=n8n_admin
POSTGRES_PASSWORD=choose_a_strong_password

# n8n security: generate a random string for encryption
N8N_ENCRYPTION_KEY=your_random_string_here
```

### 3. Deploy
Launch the entire stack in the background:
```bash
docker compose up -d
```
To check logs and ensure services is running properly:
```bash
docker-compose -f docker-compose.dev.yaml logs -f
```
To shut down services:
```bash
docker-compose -f docker-compose.dev.yaml down
```

## Architecture

This stack is designed for reliability and performance:
* **Traefik v2.10**: Acts as a reverse proxy and handles SSL certificates automatically.
* **n8n (Main)**: The primary web interface and workflow designer.
* **n8n-worker**: A dedicated service to handle heavy execution tasks, keeping the UI responsive.
* **PostgreSQL 16**: Persistent storage for workflows and execution data.
* **Redis**: High-performance message broker for the n8n queue mode.

# Dev environment
You can also run a lightweight version of n8n locally (SQLite, no workers, no Traefik).

## 1. Basic Launch
Ensure project folder is created (`git clone`) and you have a `.env` file.
```bash
docker compose -f docker-compose.dev.yaml up -d
```
Access n8n at `http://localhost:5678`.

## 2. Using Webhooks (Ngrok)
If you need to test external webhooks:

1. Start ngrok on the n8n port:
```bash
ngrok http 5678
```
2. Copy the HTTPS URL provided by ngrok (e.g., https://xxxx.ngrok-free.app).
3. Update your `.env` file:
```
WEBHOOK_URL=https://xxxx.ngrok-free.app
```
4. Apply changes by re-running the stack:
```
docker compose -f docker-compose.dev.yaml up -d
```
