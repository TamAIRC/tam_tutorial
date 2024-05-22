# Docker in Centos 9

This guide provides instructions on how to set up and run GitLab on CentOS 9 using Docker. Two methods are covered:

1. Using docker run command.
2. Using Docker Compose.

## Prerequisites

- CentOS 9
- Docker installed on your system

## Installing Docker on CentOS 9

Follow these steps to install Docker on CentOS 9:

**1. Remove conflict packages with Docker (Podman) and runc packages:**

```bash
sudo dnf -y remove podman runc
```

**2. Set up the Docker repository:**

- Download Docker repository file:

```bash
sudo curl -fsSL https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
```

- Disable the repository by default:

```bash
sudo sed -i -e "s/enabled=1/enabled=0/g" /etc/yum.repos.d/docker-ce.repo
```

**3. Install Docker Engine:**

```bash
sudo dnf --enablerepo=docker-ce-stable -y install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

**4. Start and enable Docker:**

```bash
sudo systemctl enable --now docker
```

**5. Verify the installation:**

```bash
sudo docker --version
```
