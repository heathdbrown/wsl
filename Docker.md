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
  dnf install bind-utils
  ```

# References
- [Docker on WSL](https://dev.to/bowmanjd/install-docker-on-windows-wsl-without-docker-desktop-34m9)
- [Fedora install on WSL](https://dev.to/bowmanjd/install-fedora-on-windows-subsystem-for-linux-wsl-4b26)
- [Proxy Settings for Fedora](https://www.cyberciti.biz/faq/how-to-use-dnf-command-with-a-proxy-server-on-fedora/)
