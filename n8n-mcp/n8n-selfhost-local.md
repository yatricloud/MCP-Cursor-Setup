

# Local Installation: n8n on Your Machine (HTTP)

## 1. Update System
```sh
sudo apt update && sudo apt upgrade -y
```

## 2. Install Docker and docker-compose
```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
# Log out and back in to apply Docker group changes
sudo apt install -y docker-compose
```

## 3. Set Up n8n Project Directory
```sh
cd ~
mkdir -p n8n
cd n8n
```

## 4. Create docker-compose.yml
Example:
```yaml
version: '3.1'

services:
  n8n:
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    env_file:
      - .env
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

Create a `.env` file in the same directory with your environment variables, for example:
```env
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=yourStrongPassword
N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http
N8N_EDITOR_BASE_URL=http://localhost:5678/
WEBHOOK_URL=http://localhost:5678/
N8N_SECURE_COOKIE=false
N8N_RUNNERS_ENABLED=true
```

## 5. Start n8n
```sh
sudo docker-compose up -d
```

## 6. Access n8n
- Open your browser and go to: http://localhost:5678
- Log in with your credentials. 