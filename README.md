# Documentation

## See the full documentation on https://documentation-com.gitbook.io/wazuh-installation/

# Prerequisites


## System

```
Ubuntu 22.04
or
Debian 12
```

## System Update

```bash
sudo apt update && sudo apt full-upgrade -y
```

## Git Installation

```bash
sudo apt install git -y
```

## Curl Installation

```bash
sudo apt install curl -y
```

## Install Required Packages

```bash
sudo apt install apt-transport-https ca-certificates software-properties-common -y
```

## Docker Installation - Ubuntu

### **Add Docker APT Key**

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
```

### **Add Docker APT Repository**

```bash
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

## Docker Installation - Debian

### **Add Docker APT Key**

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
```

### **Add Docker APT Repository**

```bash
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

## **Install Docker**

```bash
sudo apt-get update
sudo apt-get install docker-ce -y
```

## **Download and Install Docker Compose**

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose
```

## Ansible Installation

```bash
sudo apt install ansible -y
```

# Wazuh Docker Deployment

## Single-node Deployment

### Clone the Repository

1. Clone the Wazuh repository to your system:

```bash
git clone https://github.com/wazuh/wazuh-docker.git -b v4.4.5
```

Then enter into the <mark style="color:red;">`single-node`</mark> directory to execute all the commands described below within this directory with the command <mark style="color:red;">`cd wazuh-docker/single-node/`</mark>.

### Configuration of the docker-compose file

1. If we look at the file <mark style="color:red;">`docker-compose.yml`</mark> with the command <mark style="color:red;">`nano docker-compose.yml`</mark> we see that the dashboard is running in the port <mark style="color:red;">**443**</mark> and the default credentials are <mark style="color:red;">**(admin/SecretPassword)**</mark>, that you can change in the <mark style="color:red;">`docker-compose.yml`</mark> file.

```yaml
wazuh.dashboard:
  image: wazuh/wazuh-dashboard:4.8.0
  hostname: wazuh.dashboard
  restart: always
  ports:
    - 443:5601
  environment:
    - INDEXER_USERNAME=admin
    - INDEXER_PASSWORD=SecretPassword
```

### Generate Certificates

1. Provide a group of certificates for each node in the stack to secure communication between the nodes:

```bash
docker-compose -f generate-indexer-certs.yml run --rm generator
```

This saves the certificates into the <mark style="color:red;">`config/wazuh_indexer_ssl_certs`</mark> directory.

### Docker Compose

1. Start the Wazuh single-node deployment using docker-compose:

```bash
docker-compose up -d
```

2. After that navigate to the dashboard with <mark style="color:red;">`https://your_ip_address`</mark>.
