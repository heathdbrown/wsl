# Docker on WSL
Moving away from Docker Desktop on Windows to running Docker inside of WSL.

# Using Ubuntu 20.04
- cleanup packages
  ```bash
  sudo apt remove docker wmdocker docker.io containerd runc -yq
  sudo apt autoremove -yq
  ```
- install dependencies
  ```bash
  sudo apt install --no-install-recommends apt-transport-https ca-certificates curl gnupg2 -yq
  ```
- source /etc/os-release
  ```bash
  source /etc/os-release
  ```
- download and install repo
  ```
  curl -fsSL https://download.docker.com/linux/${ID}/gpg | sudo apt-key add -
  ```
- Add update repo
  ```bash
  echo "deb [arch=amd64] https://download.docker.com/linux/${ID} ${VERSION_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/docker.list
  sudo apt update
  ```
- Install docker
  ```bash
  sudo apt install docker-ce docker-ce-cli containerd.io -yq
  ```
- Add user to docker group
  ```bash
  sudo usermod -aG docker $USER
  ```
- Steps for groups so the socket is shared
  > read the references section for installing docker in WSL
  ```bash
  # view group ids
  getent group | cut -d: -f3 | grep -E '^[0-9]{4}' | sort -g
  # check for id you want to use is free
  getent group | grep 36257 || echo "Yes, that ID is free"
  # modify group with no groupmod 
  # sudo sed -i -e 's/^\(docker:x\):[^:]\+/\1:36257/' /etc/group
  # modify group with groupmod
  sudo groupmod -g 36257 docker
  ```
- Configure dockerd
  ```bash
  DOCKER_DIR=/mnt/wsl/shared-docker
  mkdir -pm o=,ug=rwx "$DOCKER_DIR"
  chgrp docker "$DOCKER_DIR"
  ```
-  Create /etc/docker/daemon.json
  ```json
  {
    "hosts": ["unix:///mnt/wsl/shared-docker/docker.sock"]
  }
  ```
- If behind a proxy, set proxy vars in ~/.bashrc and load environment vars when starting dockerd
   ```bash
   # function setproxy is  in ~/.bashrc which sets http_proxy,  https_proxy, no_proxy
   setproxy
   sudo -E dockerd
   ```
 - in another session run docker client
   ```bash
   docker -H unix:///mnt/wsl/shared-docker/docker.sock run --rm hello-world
   ```
   
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
-  Add repo if using fedora
  ```
  sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo

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
   # set proxy if needed and run with env  vars
   # setproxy is a function in .bashrc that sets http_proxy, https_proxy, and no_proxy
   setproxy
   sudo -E dockerd
  ```
 - Run docker
   ```
   docker -H unix:///mnt/wsl/shared-docker/docker.sock run --rm hello-world
   ```
# Optional items to make life easier
> Most of these items are covered in Docker on WSL in the References section.

## Passwordless for dockerd startup and Environment passing
To make life easier, NOT more secure, we can allow the dockerd to be issued with NOPASSWD and with SETENV.

The SETENV is needed to allow for `sudo -E` to be allowed to pass the users local environment variables to root, which is where we set the proxy settings.

```bash
sudo visdo
# Add line
%docker ALL=(ALL)  NOPASSWD:SETENV: /usr/bin/dockerd
```

## Setup easy aliases

```bash
# Verify that ~/.bashrc or ~/.profile loads in ~/.bash_aliases if seen
 grep -n aliases ~/.bashrc ~/.profile
/home/$USER/.bashrc:75:# enable color support of ls and also add handy aliases
/home/$USER/.bashrc:90:# some more ls aliases
/home/$USER/.bashrc:101:# ~/.bash_aliases, instead of adding them here directly.
/home/$USER/.bashrc:104:if [ -f ~/.bash_aliases ]; then
/home/$USER/.bashrc:105:    . ~/.bash_aliases
# Edit and add aliases
vim ~/.bash_alaises
# aliases
alias dockerdup='sudo -E dockerd'
alias dockerc='docker -H unix:///mnt/wsl/shared-docker/docker.sock'
```

# References
- [Docker on WSL](https://dev.to/bowmanjd/install-docker-on-windows-wsl-without-docker-desktop-34m9)
- [Fedora install on WSL](https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26)
- [Proxy Settings for Fedora](https://www.cyberciti.biz/faq/how-to-use-dnf-command-with-a-proxy-server-on-fedora/)
