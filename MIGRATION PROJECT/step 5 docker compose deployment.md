Step 5: Full-Stack Deployment with Docker Compose

## Objective

Run the JerseyHub frontend and backend together on the Sydney EC2 instance with Docker Compose.

~~~text
Browser :80 -> Nginx frontend -> /api -> Express backend :2987 -> MongoDB Atlas
~~~

## Starting point

- Ubuntu EC2 instance in Sydney (ap-southeast-2)
- Docker Engine and Docker Compose installed
- Backend image: jerseyhub-backend:1.0
- Frontend image: jerseyhub-frontend:1.0
- MongoDB Atlas connected
- Nginx forwarding /api/ to http://backend:2987/

## 1. Understand the Nginx upstream

~~~nginx
location /api/ {
    proxy_pass http://backend:2987/;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
~~~

The standalone frontend test returned "host not found in upstream backend". This was expected because backend is a Compose service name resolved only on the shared Compose network.

## 2. Create compose.yaml

~~~yaml
services:
  backend:
    build:
      context: ./backend
    image: jerseyhub-backend:1.0
    env_file:
      - ./backend/.env
    expose:
      - "2987"
    restart: unless-stopped

  frontend:
    build:
      context: ./frontend
      args:
        VITE_API_URL: /api
        VITE_RAZORPAY_ENABLED: "false"
    image: jerseyhub-frontend:1.0
    ports:
      - "80:80"
    depends_on:
      - backend
    restart: unless-stopped
~~~

## Important settings

| Setting | Purpose |
|---|---|
| env_file | Supplies private backend configuration at runtime. |
| expose: 2987 | Makes Express reachable inside Docker without publishing it publicly. |
| ports: 80:80 | Publishes Nginx on the EC2 HTTP port. |
| depends_on | Starts the backend before the frontend. |
| restart: unless-stopped | Restarts services after failure or reboot unless deliberately stopped. |
| VITE_API_URL: /api | Sends browser API calls through Nginx on the same origin. |

## 3. Validate and replace the old container

~~~bash
docker compose config
docker stop jerseyhub-backend
docker rm jerseyhub-backend
~~~

The validation succeeded. Its expanded output can reveal values from .env, so do not publish it. Removing the old container preserved the image, repository, environment file and database.

## 4. Build and start the stack

~~~bash
docker compose up -d --build
~~~

Compose created jerseyhub-devops_default and started jerseyhub-devops-backend-1 and jerseyhub-devops-frontend-1.

## 5. Verify status and logs

~~~bash
docker compose ps
docker compose logs --tail 50 backend
docker compose logs --tail 50 frontend
~~~

Verified results:

~~~text
Backend:  Up, internal port 2987
Frontend: Up, 0.0.0.0:80 -> 80/tcp
MongoDB Atlas Connected
Server running on port 2987
Nginx worker processes started
~~~

The Gmail authentication warning was expected because temporary infrastructure-test credentials were used. It did not stop the API.

## 6. Test the deployment

~~~bash
docker compose exec frontend nginx -t
curl -I http://127.0.0.1/
curl -i http://127.0.0.1/api/
~~~

Expected API response:

~~~text
Football Jersey Store API Running
~~~

The public application loaded successfully at http://15.135.221.156.

## 7. Commit and push

~~~bash
git add compose.yaml
git commit -m "Add Docker Compose deployment"
git push origin main
~~~

The configuration was pushed successfully in commit 68fff56.

## Useful operating commands

~~~bash
docker compose ps                    # Show service status
docker compose logs -f               # Follow logs
docker compose restart               # Restart both services
docker compose down                  # Stop the stack
docker compose up -d --build         # Rebuild and start
~~~

## Result

Step 5 completed the first full-stack container deployment of JerseyHub. Compose manages both services, Nginx serves the Vite build and proxies /api, Express connects to MongoDB Atlas, and only port 80 is exposed publicly.

## Remaining production work

1. Allocate an Elastic IP or configure a domain.
2. Enable HTTPS and redirect HTTP to HTTPS.
3. Rotate credentials exposed during learning.
4. Configure Gmail or disable email delivery.
5. Add health checks and monitoring.
6. Test registration, login, catalog, cart and order workflows.

The browser shows **Not secure** because the site currently uses HTTP. This is expected until HTTPS is configured.
