# PHP with Nginx Reverse Proxy Docker Setup

This project provides a Docker setup for serving a PHP application using Nginx as a reverse proxy, with PHP-FPM as the backend. It's designed for testing deployments to Docker Swarm, Kubernetes, and Nomad.

The project includes a simple `frontend` and `backend` Docker setup, where:

- The **backend** service runs PHP-FPM.
- The **frontend** service runs Nginx, which acts as a reverse proxy to PHP-FPM to render the PHP files.

The setup is structured to allow easy communication between the two services and provides a flexible environment for testing your deployments in Docker-based orchestration platforms.

## Directory Structure

```bash
├── .docker/
│   ├── dev/
│   │   ├── frontend/
│   │   │   ├── Dockerfile       # Nginx configuration to serve PHP
│   │   │   ├── app.conf         # Nginx FastCGI configuration
│   │   ├── backend/
│   │   │   └── Dockerfile       # PHP-FPM setup
│   ├── docker-compose.yml       # Docker Compose configuration
├── src/
│   └── index.php                # PHP file served by Nginx

# Docker PHP Project Setup

## Prerequisites
Before running the project, ensure that you have the following installed:

- **Docker**: Install Docker from the official Docker website.
- **Docker Compose**: Install Docker Compose from the official Docker Compose installation guide.
- **A Docker Hub account (optional)**: If you wish to push the built images to Docker Hub, you will need a Docker Hub account.

## Services

### Frontend Service
The frontend service uses Nginx to serve PHP files. It is configured to connect to the backend service (PHP-FPM) over the network. The PHP files are processed by PHP-FPM, and the results are served via Nginx.

- **Dockerfile**: Located at `.docker/dev/frontend/Dockerfile`
- **Configuration**: The Nginx server is configured to use `fastcgi_pass` to forward requests to the backend service on port 9000 (`backend:9000`).

### Backend Service
The backend service runs PHP-FPM to process PHP scripts. It listens on port 9000, which is accessed by the frontend service.

- **Dockerfile**: Located at `.docker/dev/backend/Dockerfile`

## Communication Between Services
Both services (frontend and backend) are on the same Docker network, allowing them to communicate. The frontend service uses Nginx configured in `app.conf` to forward PHP requests to the backend service on `backend:9000`.

## Docker Compose Setup
This project uses Docker Compose to manage the services. The `docker-compose.yml` file defines both services (frontend and backend), including their build contexts and image tags.

## Build and Push Images
To build and push the Docker images to Docker Hub, you can run the following commands:

Build the images:
```bash
docker-compose build
```

Push the images to Docker Hub:
```bash
docker-compose push
```

The images are tagged as `frontend` and `backend` to differentiate them when pushing to Docker Hub.

## docker-compose.yml
Here is the relevant part of the docker-compose.yml:

```yaml
version: '3.7'

services:
  frontend:
    build:
      context: .
      dockerfile: ./.docker/dev/frontend/Dockerfile
    volumes:
      - ./src:/var/www
    restart: unless-stopped
    working_dir: /var/www
    networks:
      - dockerphp
    ports:
      - "8023:80"
    image: tayyab7891/fireyopssphp:frontend

  backend:
    build:
      context: .
      dockerfile: ./.docker/dev/backend/Dockerfile
    restart: unless-stopped
    working_dir: /var/www
    volumes:
      - ./src:/var/www
    networks:
      - dockerphp
    image: tayyab7891/fireyopssphp:backend

networks:
  dockerphp:
    driver: bridge
```

## Configuration Files

### frontend/app.conf
Nginx configuration that proxies PHP requests to backend:9000.

Example:
```nginx
server {
    listen 80;
    server_name _;
    root /var/www/;
    index index.php;

     location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass backend:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```

### backend/Dockerfile
A simple PHP-FPM setup for serving PHP files.

Example:
```dockerfile
FROM       php:8.2-fpm
WORKDIR /var/www

USER    1000
EXPOSE  9000
CMD     ["php-fpm"]
```

### frontend/Dockerfile
Nginx setup for serving PHP files.

Example:
```dockerfile
FROM    nginx:alpine
COPY    .docker/dev/frontend/app.conf   /etc/nginx/conf.d/default.conf
```

## Running the Application
1. Clone the repository and navigate to the project folder.
2. Run the following command to start the services:
```bash
docker-compose up
```
3. This will start the frontend and backend services. The frontend will be accessible at http://localhost:8023, and it will communicate with the backend to render PHP pages.

## Conclusion
This setup provides a clean, modular approach for testing Docker deployments with multiple services in Docker Swarm, Kubernetes, or Nomad. It uses Nginx as a reverse proxy to serve PHP via PHP-FPM, with easy communication between frontend and backend services using Docker networking.

## Note!
Inside docker-compose.yml you will need to change image to point to your own dockerhub or private repository, otherwise you may remove it completely if you do not intend to push any images.