# PHP with Nginx Reverse Proxy Docker Setup

This project provides a Docker setup for serving a PHP application using Nginx as a reverse proxy, with PHP-FPM as the backend. It's designed for testing deployments to Docker Swarm, Kubernetes, and Nomad.

The project includes a simple `frontend` and `backend` Docker setup, where:

- The **backend** service runs PHP-FPM.
- The **frontend** service runs Nginx, which acts as a reverse proxy to PHP-FPM to render the PHP files.

The setup is structured to allow easy communication between the two services and provides a flexible environment for testing your deployments in Docker-based orchestration platforms.

## Directory Structure

```bash
.docker/
├── dev/
│   ├── frontend/
│   │   ├── Dockerfile       # Nginx configuration to serve PHP
│   │   ├── app.conf         # Nginx FastCGI configuration
│   ├── backend/
│   │   └── Dockerfile       # PHP-FPM setup
├── docker-compose.yml       # Docker Compose configuration
