## Deploying GitLab with Docker in Centos 9

This guide provides instructions on how to set up and run GitLab on CentOS 9 using Docker. Two methods are covered:

1. Using docker run command.
2. Using Docker Compose.

## Prerequisites

- CentOS 9
- Docker installed on your system

## Deploying GitLab

### Step 1. Installing Docker on CentOS 9 (if not already installed): [Installing Docker](../README.md)

### Step 2. Download the GitLab Docker image from [Docker Hub](https://hub.docker.com/r/gitlab/gitlab-ce):

```bash
docker pull gitlab/gitlab-ce
```

### Step 3. Run the GitLab Container:

#### **Method 1: Basic Run Command**

This method involves a simpler command without additional system control parameters and user limits. Use the following command to run the GitLab container:

```bash
sudo docker run -p 8080:80 -p 8443:443 -p 8022:22 \
--restart always \
-v ./gitlab/config:/etc/gitlab \
-v ./gitlab/data:/var/opt/gitlab \
-v ./gitlab/logs:/var/log/gitlab \
-v /etc/localtime:/etc/localtime \
gitlab/gitlab-ce:latest
```

#### **Method 1: Using docker run**

```bash
sudo docker run --detach \
--sysctl net.core.somaxconn=1024 \
--ulimit sigpending=62793 \
--ulimit nproc=131072 \
--ulimit nofile=60000 \
--ulimit core=0 \
--publish 8443:443 \
--publish 8080:80 \
--publish 8022:22 \
--restart always \
--volume ./gitlab/config:/etc/gitlab:z \
--volume ./gitlab/logs:/var/log/gitlab:z \
--volume ./gitlab/data:/var/opt/gitlab:z \
--volume /etc/localtime:/etc/localtime \
gitlab/gitlab-ce:latest
```

- **Explanation of the docker run command**
  - `--sysctl net.core.somaxconn=1024`: Sets the maximum number of connections that can be queued.
  - `--ulimit sigpending=62793`, `--ulimit nproc=131072`, `--ulimit nofile=60000`, `--ulimit core=0`: Set user limits for the container.
  - `--publish`: Maps host ports to container ports (8443:443 for HTTPS, 8080:80 for HTTP, 8022:22 for SSH).
  - `--restart always`: Ensures the container always restarts if it stops.
  - `--volume`: Mounts host directories to the container with SELinux shared labeling (:z).

#### **Method 2: Using Docker Compose**

**1. Install Docker Compose (if not already installed):**

```bash
curl -L https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

```bash
chmod 755 /usr/local/bin/docker-compose
```

```bash
docker-compose --version
```

**2. Create a docker-compose.yml file with the following content:**

```yaml
version: "3.8"

services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab-server
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://localhost'
        gitlab_rails['initial_root_password'] = 'airc@202$'
        puma['worker_processes'] = 0 # disable cluster mode to avoid more memory usage
        nginx['redirect_http_to_https'] = true
        nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.crt"
        nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.key"
    ports:
      - "8443:443"
      - "8080:80"
      - "8022:22"
    restart: always
    volumes:
      - ./gitlab/config:/etc/gitlab:z
      - ./gitlab/logs:/var/log/gitlab:z
      - ./gitlab/data:/var/opt/gitlab:z
      - /etc/gitlab/ssl/gitlab.crt:/etc/gitlab/ssl/gitlab.crt:ro
      - /etc/gitlab/ssl/gitlab.key:/etc/gitlab/ssl/gitlab.key:ro
      - /etc/localtime:/etc/localtime
    sysctls:
      net.core.somaxconn: 1024
    ulimits:
      sigpending: 62793
      nproc: 131072
      nofile:
        soft: 60000
        hard: 60000
      core: 0

```

**3. Start the GitLab container using Docker Compose:**

```bash
sudo docker-compose up -d
```

**Explanation of the docker-compose.yml file**

- version: '3.8': Specifies the version of the Docker Compose file format.
- services: Defines the services that make up the application.
- gitlab: The name of the GitLab service.
- image: gitlab/gitlab-ce:latest: Specifies the Docker image to use.
- container_name: gitlab: Names the container (optional).
- ports: Maps host ports to container ports.
- restart: always: Ensures the container always restarts if it stops.
- volumes: Mounts host directories to the container with SELinux shared labeling.
- sysctls: Sets system control parameters for the container.
- ulimits: Sets user limits for the container.
