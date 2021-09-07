# Reference URLS
 
Traefik Docs:  
https://doc.traefik.io/traefik/

Network Architecture (OpenShift Docs):  
https://docs.openshift.com/container-platform/4.7/installing/installing_bare_metal_ipi/ipi-install-overview.html


# Installating Traefik

1. Update the template with your:

- Hostname
- Cluster name
- Cluster Private IP addresses (Cluster API, Ingress)

  Option 1: Run on Docker

  Supported on the platforms in this URL: https://docs.docker.com/engine/install/

### Instructions for CentOS and other non-RHEL systems:

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

### Installing on RHEL8


1. Install traefik

```console
curl -LO https://github.com/traefik/traefik/releases/download/v2.5.1/traefik_v2.5.1_linux_amd64.tar.gz
tar zxvf traefik_v2.5.1_linux_amd64.tar.gz
sudo mv traefik /usr/local/bin
sudo mkdir -p /etc/traefik
sudo mkdir -p /etc/traefik/conf.d
```

1. Create the following Systemd service file and enable the service

```console
touch /etc/systemd/system/traefik.service
```

1. Copy and paste the contents of the traefik.service file in the templates directory of this repository

1. `cd /etc/traefik/`

1. create a traefik.yaml file and paste the contents of the template

1. start and enable the service

```console
systemctl start traefik.service  
systemctl enable traefik.service
```

1. Verify the successful deployment of the traefik service:  

```console
systemctl status traefik.service  
```

1. Update your DNS with the following entries:

```console
*.YOUR-CLUSTER-NAME.YOUR-DOMAIN      A	  YOUR_PROXY_SERVER_PUBLIC_IP_ADDRESS
*.apps.YOUR-CLUSTER-NAME.YOUR-DOMAIN   A	  YOUR_PROXY_SERVER_PUBLIC_IP_ADDRESS
```

**NOTE:** the wildcard subdomain declaration at the cluster domain level (*.mas-sandbox-ep.democore.cloud) is preferred in this case, since we are also hosting the traefik dashboard at traefik.YOUR-CLUSTER-NAME.YOUR-DOMAIN

1. The service is now operational. To view a list of your virtual routers and services, navigate to the Traefik service dashboard:  
   
  https://traefik.YOUR-CLUSTER-NAME.YOUR-DOMAIN/dashboard/


Because Traefik routes requests made from the public domain to the openshift cluster in the private domain, you may also disconnect from the VPN and access your OpenShift cluster console through your cluster's console URL:  

  https://console-openshift-console.apps.YOUR-CLUSTER-NAME.YOUR-DOMAIN/dashboard/