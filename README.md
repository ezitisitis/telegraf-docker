# Telegraf Docker Monitoring Setup

This repository contains a Docker Compose configuration for running Telegraf, configured to be scraped by a remote Prometheus instance. It includes system-level monitoring and Docker container monitoring via the Docker socket.

## Features

- **System Monitoring**: CPU, memory, disk, processes, swap, and system metrics.
- **Docker Monitoring**: Monitors Docker container stats by mounting `/var/run/docker.sock`.
- **Prometheus Client Output**: Exposes metrics on port `9273` in Prometheus format, ready for scraping.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Quick Start

```bash
  git clone https://github.com/ezitisitis/telegraf-docker.git
  cd telegraf-docker
  cp telegraf.conf.example telegraf.conf
  echo "DOCKER_GID=$(getent group docker | cut -d: -f3)" > .env
  docker compose up -d
  echo "Add this as your prometheus target: $(hostname -I | awk '{print $1}'):9273"
```

Once running, metrics are available at:
`http://<host-ip>:9273/metrics`

You can configure your remote Prometheus instance to scrape this target:

```yaml
scrape_configs:
  - job_name: 'telegraf'
    static_configs:
      - targets: ['<host-ip>:9273']
```

## Security Note

In this setup, Telegraf runs as the **root** user (`user: "0:0"`) to gain read access to `/var/run/docker.sock`. In a production environment, it is recommended to manage permissions more granularly (e.g., by creating a `docker` group and adding the `telegraf` user to it) if possible.
