# Nginx Reverse Proxy

A reverse proxy setup using Nginx to route incoming HTTP traffic to a Flask backend application. The proxy listens on port 8080 and forwards requests to the Flask app running on port 5000.

## Prerequisites

- Docker installed
- Flask app container running with name `flask-container` on port 5000

To start your Flask app:
```bash
docker run -d --name flask-container -p 5000:5000 flask-app
```

## Project Structure

- `nginx.conf` - Nginx configuration with upstream backend, security headers, and health check endpoint
- `Dockerfile` - Multi-stage build with non-root user and health checks
- `docker-compose.yml` - Orchestrates nginx-proxy and links to existing flask-container
- `.dockerignore` - Excludes unnecessary files from Docker build context

## How It Works

1. Client sends request to `http://localhost:8080`
2. Nginx receives the request and forwards it to flask-container on port 5000
3. Flask app processes the request and returns response
4. Nginx sends the response back to the client

## Getting Started

### Clone the Repository

```bash
git clone <repository-url>
cd reverse_proxy/nginx-proxy
```

### Start the Nginx Proxy

```bash
docker-compose up -d
```

This command will:
- Build the Nginx image with custom configuration
- Link to the existing flask-container
- Expose port 8080 for external access

### Verify It's Running

```bash
# Check health endpoint
curl http://localhost:8080/health

# Access the Flask application through the proxy
curl http://localhost:8080
```

### Stop the Service

```bash
docker-compose down
```

## Docker Best Practices Implemented

- Non-root user execution for security
- Specific version tags (nginx:1.25-alpine) instead of latest
- Security headers (X-Frame-Options, X-Content-Type-Options, X-XSS-Protection)
- Health checks for container monitoring
- Restart policy (unless-stopped) for reliability
- Minimal Alpine base image for smaller footprint
- .dockerignore for optimized build context
- Proper signal handling (SIGQUIT) for graceful shutdown

## Configuration Details

### Nginx Configuration

- Upstream backend points to flask-container:5000
- Security headers added to all responses
- Connection timeouts set to 60 seconds
- Health check endpoint at `/health`
- Proxy headers forwarded (Host, X-Real-IP, X-Forwarded-For)

### Docker Compose

- Uses external_links to connect to existing flask-container
- Health checks configured for monitoring
- Bridge network mode for container communication
