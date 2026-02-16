# Nginx Reverse Proxy

This project runs an Nginx reverse proxy on port `8080` and forwards traffic to a backend container on port `5000`.

Current backend target:
- Container name: `study-planner`
- Published port: `5000:5000`

## Prerequisites

- Docker and Docker Compose plugin installed
- Backend container already running (example: `study-planner` on port `5000`)

## Project Structure

- `nginx-proxy/nginx.conf` - Nginx upstream, proxy rules, security headers, and `/health` endpoint
- `nginx-proxy/Dockerfile` - Hardened Nginx image (non-root user, health check, graceful stop)
- `nginx-proxy/docker-compose.yml` - Runs `nginx-proxy` and links to `study-planner`
- `nginx-proxy/.dockerignore` - Reduces Docker build context

## How It Works

1. Client sends request to `http://localhost:8080`
2. Nginx receives the request
3. Nginx proxies it to `host.docker.internal:5000`
4. Backend response is returned to the client

## Backend Example

If your image is already built as `study-planner`, run:

```bash
docker run -d --name study-planner -p 5000:5000 study-planner
```

Verify backend directly:

```bash
curl http://localhost:5000
```

## Run the Proxy

From the repository root:

```bash
cd nginx-proxy
docker compose up -d --build
```

## Verify

```bash
# Nginx health
curl http://localhost:8080/health

# App through reverse proxy
curl http://localhost:8080
```

## Stop

```bash
cd nginx-proxy
docker compose down
```

## Configuration Notes

- Nginx upstream is set in `nginx-proxy/nginx.conf` as `host.docker.internal:5000`
- Proxy container exposes host port `8080` to container port `80`
- Compose currently references `study-planner` via `external_links`
