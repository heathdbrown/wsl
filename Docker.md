# Docker on WSL
Moving away from Docker Desktop on Windows to running Docker inside of WSL.

# Using Fedora rootfs you will need to make some adjustments

- proxy support in  /etc/dns/dnf.conf
  ```bash
  vi /etc/dnf/dnf.config
  proxy=http://proxy.proxy.com:80
  ```
- install bind-utils for testing network configuration
  ```bash
  dnf install bind-utils  -yq
  ```
- install passwd and cracklib-dicts to create non-root user
  ```bash
  dnf install passwd cracklib-dicts -yq
  useradd -G wheel <username>
  passwd <username>
  ```
- Configure dockerd
  ```bash
  DOCKER_DIR=/mnt/wsl/shared-docker
  mkdir -pm o=,ug=rwx "$DOCKER_DIR"
  chgrp docker "$DOCKER_DIR"
  ```
- Add /etc/docker/daemon.json and configuration
  ```json
  {
  "hosts": ["unix:///mnt/wsl/shared-docker/docker.sock"]
  }
  ```
- Add in docker configuration for proxy
  ```bash
  sudo mkdir -p /root/.docker
  mkdir -p ~/.docker
  sudo touch /root/.docker/config.json
  touch ~/.docker/config.json
  ```
- Configuration in config.json
  ```json
  {
    "proxies": {
        "default": {
            "httpProxy": "http://proxy.proxy.com:80",
            "httpsProxy": "http://proxy.proxy.com:80",
            "noProxy": "docker:2375,docker:2376,localhost,127.0.0.1,localaddress,.localdomain.com"
        }
    }
  }
  ```
- start dockerd
  ```bash
   sudo dockerd
  ```
 - Run docker
   ```
   docker -H unix:///mnt/wsl/shared-docker/docker.sock run --rm hello-world
   ```
# References
- [Docker on WSL](https://dev.to/bowmanjd/install-docker-on-windows-wsl-without-docker-desktop-34m9)
- [Fedora install on WSL](https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26)
- [Proxy Settings for Fedora](https://www.cyberciti.biz/faq/how-to-use-dnf-command-with-a-proxy-server-on-fedora/)
