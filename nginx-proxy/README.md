# Nginx Reverse Proxy

A reverse proxy setup using Nginx to route incoming HTTP traffic to a Flask backend application. The proxy listens on port 8080 and forwards requests to the Flask app running on port 5000.

## Project Structure

- `nginx.conf` - Nginx configuration with upstream backend, security headers, and health check endpoint
- `Dockerfile` - Multi-stage build with non-root user and health checks
- `docker-compose.yml` - Orchestrates both nginx-proxy and flask-app containers
- `.dockerignore` - Excludes unnecessary files from Docker build context

## How It Works

1. Client sends request to `http://localhost:8080`
2. Nginx receives the request and forwards it to the Flask app container
3. Flask app processes the request and returns response
4. Nginx sends the response back to the client

## Getting Started

### Clone the Repository

```bash
git clone <repository-url>
cd reverse_proxy/nginx-proxy
```

### Start the Services

```bash
docker-compose up -d
```

This command will:
- Build the Nginx image with custom configuration
- Start the Flask application container
- Create a Docker network for container communication
- Expose port 8080 for external access

### Verify It's Running

```bash
# Check health endpoint
curl http://localhost:8080/health

# Access the Flask application through the proxy
curl http://localhost:8080
```

### Stop the Services

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

- Upstream backend points to flask-app:5000
- Security headers added to all responses
- Connection timeouts set to 60 seconds
- Health check endpoint at `/health`
- Proxy headers forwarded (Host, X-Real-IP, X-Forwarded-For)

### Docker Compose

- Both containers run on the same network (app-network)
- Nginx depends on Flask app to ensure proper startup order
- Volumes mount Flask code for easy development
- Health checks configured for both services
