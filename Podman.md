# Podman on WSL

## Install Kubic for Ubuntu

```
. /etc/os-release
sudo sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/x${NAME}_${VERSION_ID}/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list"
wget -nv https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/x${NAME}_${VERSION_ID}/Release.key -O Release.key
sudo apt-key add - < Release.key
sudo apt-get update -qq
sudo apt-get -qq -y install podman
sudo mkdir -p /etc/containers
echo -e "[registries.search]\nregistries = ['docker.io', 'quay.io']" | sudo tee /etc/containers/registries.conf
```

## Modify Podman configuration for WSL for rootless configuration

```
mkdir -p $HOME/.config/containers
podman info > $HOME/.config/containers/libpod.conf
vim $HOME/.config/containers/libpod.conf
```

Make sure that eventLogger is set to file.

# Reference

Source: https://www.redhat.com/sysadmin/podman-windows-wsl2
