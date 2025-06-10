# BlockScout Docker Integration

For detailed instructions on Docker deployment, environment variables, and customization options, refer to the official [Docker Integration Documentation](https://docs.blockscout.com/for-developers/deployment/docker-compose-deployment).

---

## 🚀 Launch the Application with a Specific Version

To start the application with specific Docker tags, use the following command:

```bash
cd docker
make start FRONTEND_DOCKER_TAG=v1.38.1 STATS_DOCKER_TAG=v2.8.0 DOCKER_TAG=8.0.1
```
To start the application with latest Docker tags,
```bash
cd docker
make start 
```

### Deployed Services and Their URLs:
| Service   | URL |
| -------- | ------- |
| Backend API | http://localhost:8001    |
| Stats API    | http://localhost:8080   |
| Frontend  | http://localhost:8000  |


## Setup domain
### To configure the domain for the backend
```bash
cd /etc/nginx/sites-enabled/
sudo nano backend.conf
```
```bash
map $http_upgrade $connection_upgrade {

  default upgrade;
  ''      close;
}

server {
    listen 80;
    server_name backend-testnet.myourdomain.com;


    location / {
        proxy_pass http://127.0.0.1:8001;  # proxy container
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header      Upgrade "$http_upgrade";
        proxy_set_header      Connection $connection_upgrade;
        proxy_cache_bypass    $http_upgrade;
    }
}
```

### To configure the domain for stats api
```bash
cd /etc/nginx/sites-enabled/
sudo nano stats.conf
```
```bash
server {
    listen 80;
    server_name  stats-testnet.myourdomain.com;

    location / {
        proxy_pass http://localhost:8080;  # proxy container
    }
}
```

### To configure the domain for the frontend

```bash
cd /etc/nginx/sites-enabled/
sudo nano frontend.conf
```
```bash
server {
    server_name testnet.myourdomain.com;

    location / {
        proxy_pass http://localhost:8000;  # proxy container
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }
}
```
## Setting Up Free SSL with Let’s Encrypt
```bash
sudo certbot --nginx -d yourdomain.com www.yourdomain.com
sudo certbot --nginx -d backend-testnet.yourdomain.com # for backend
sudo certbot --nginx -d stats-testnet.yourdomain.com # for stats
sudo certbot --nginx -d new-testnet.yourdomain.com # for frontend
```