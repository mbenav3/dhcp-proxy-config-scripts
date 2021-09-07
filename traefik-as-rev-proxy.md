# Reference URLS
 
Architecture: https://docs.openshift.com/container-platform/4.7/installing/installing_bare_metal_ipi/ipi-install-overview.html

# Installation Instructions

1. Update the template with your:

- Hostname
- Cluster name
- Private IPs (Cluster API, Ingress)

Option 1: Run on Docker

Supported on the platforms in this URL: https://docs.docker.com/engine/install/

Instructions for CentOS:

First install Docker engine
**https://docs.docker.com/engine/install/centos/**


1. Create your traefik configuration directories

```console
mkdir -p /opt/traefik

touch /opt/traefik/docker-compose.yaml
touch /opt/traefik/acme.json && chmod 600 /opt/traefik/acme.json
touch /opt/traefik/traefik.yaml

touch /opt/traefik/components.yaml
```

1. Update the files with the templates included in this repository.


1. Start the trefik service

```console
docker-compose --verbose -f /opt/traefik/docker-compose.yaml up -d --remove-orphans --force-recreate
```