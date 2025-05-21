Hi, ![](https://user-images.githubusercontent.com/18350557/176309783-0785949b-9127-417c-8b55-ab5a4333674e.gif) my name is Serhii Shypylov
=========================================================================================================================================

-------------------------------

## 🛠️ Technologies & Tools
<div align="left">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linux/linux-original.svg" height="40" alt="Linux logo" />
  <img width="12" />
  <img src="https://cdn.simpleicons.org/gnubash/4EAA25" height="40" alt="Bash logo" />
  <img width="12" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-original.svg" height="40" alt="Docker logo" />
  <img width="12" />
</div>

## 🌐 Connect with Me
<p align="left">
  <a href="https://t.me/OenITConsultant_bot">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/telegram-app.png" alt="Telegram" width="30" height="30" />
  </a>
  <a href="https://www.linkedin.com/in/sergey-shipilov-7262a31b4/">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/linkedin.png" alt="LinkedIn" width="30" height="30" />
  </a>
  <a href="https://www.instagram.com/shipssvpl/">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/instagram-new.png" alt="Instagram" width="30" height="30" />
  </a>
  <a href="https://www.facebook.com/profile.php?id=100083345006373">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/facebook.png" alt="Facebook" width="30" height="30" />
  </a>
  <a href="https://discord.com/invite/6z5EyagDyW?ref=1it.pro">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/discord.png" alt="Discord" width="30" height="30" />
  </a>
  <a href="mailto:admin@mail.1it.pro">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/new-post.png" alt="Email" width="30" height="30" />
  </a>
  <a href="https://1it.pro/">
    <img src="https://img.icons8.com/ios-glyphs/30/ffffff/domain.png" alt="Website" width="30" height="30" />
  </a>
</p>

---

# OpenProject Installation with Docker Compose

This repository provides a Docker Compose configuration for deploying [OpenProject](https://www.openproject.org/), an open-source project management tool, with a PostgreSQL database.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Docker Compose Configuration](#docker-compose-configuration)
- [Environment Variables](#environment-variables)
- [License](#license)

## Prerequisites
- **Docker** and **Docker Compose** installed on your system.
- A domain or IP address for `OPENPROJECT_HOST_NAME`.
- A secure password for `POSTGRES_PASSWORD` and `OPENPROJECT_SECRET_KEY_BASE`.
- (Optional) SMTP server details for email notifications.

## Installation
1. Clone this repository:
   ```bash
   git clone https://github.com/Shipssv83/openproject-docker.git
   cd openproject-docker
   ```
2. Create a `.env` file with the required environment variables (see [Environment Variables](#environment-variables)).
3. Run Docker Compose:
   ```bash
   docker-compose up -d
   ```
4. Access OpenProject at `http://<OPENPROJECT_HOST_NAME>:<PORT>` (default: `http://localhost:8080`).
5. Log in with the default admin credentials: `admin/admin` (change immediately).

## Docker Compose Configuration
The `docker-compose.yml` file sets up two services:
- **db**: PostgreSQL database for OpenProject.
- **web**: OpenProject application server.

```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=openproject
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=openproject
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U openproject"]
      interval: 10s
      timeout: 5s
      retries: 5
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 1G
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    restart: unless-stopped
    networks:
      - openproject-network

  web:
    image: openproject/openproject:15
    depends_on:
      db:
        condition: service_healthy
    volumes:
      - opdata:/var/openproject/assets
    environment:
      - OPENPROJECT_HOST__NAME=${OPENPROJECT_HOST_NAME}
      - OPENPROJECT_HTTPS=${OPENPROJECT_HTTPS}
      - OPENPROJECT_SECRET_KEY_BASE=${OPENPROJECT_SECRET_KEY_BASE:-your_secure_key}
      - DATABASE_URL=postgres://openproject:${POSTGRES_PASSWORD}@db/openproject
      - RAILS_ENV=production
      - RAILS_MIN_THREADS=4
      - RAILS_MAX_THREADS=16
      - RAILS_CACHE_STORE=redis
      - APACHE_SERVER_NAME=${OPENPROJECT_HOST_NAME}
      - APACHE_PORT=80
    ports:
      - "${PORT}:80"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:80"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 120s
    deploy:
      resources:
        limits:
          cpus: "3.0"
          memory: 6G
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    restart: unless-stopped
    networks:
      - openproject-network

volumes:
  pgdata:
    name: openproject_pgdata
  opdata:
    name: openproject_opdata

networks:
  openproject-network:
    driver: bridge
```

## Environment Variables
Create a `.env` file in the project root with the following variables:

```bash
# OpenProject Configuration
OPENPROJECT_HOST_NAME=openproject.example.com
OPENPROJECT_HTTPS=true
OPENPROJECT_SECRET_KEY_BASE=your_secure_key_here
PORT=8080

# PostgreSQL Configuration
POSTGRES_PASSWORD=your_secure_password_here

# Optional: Email Configuration (uncomment and configure for production)
# OPENPROJECT_SMTP__ADDRESS=smtp.example.com
# OPENPROJECT_SMTP__PORT=465
# OPENPROJECT_SMTP__USERNAME=username@example.com
# OPENPROJECT_SMTP__PASSWORD=your_smtp_password
# OPENPROJECT_SMTP__ENABLE__STARTTLS__AUTO=true
```

- Replace `your_secure_key_here` and `your_secure_password_here` with strong, unique values.
- Update `OPENPROJECT_HOST_NAME` with your domain or IP.
- Configure SMTP settings for email notifications in production.

## License
This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).

---

Show some 💜 by starring this repository!