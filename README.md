# wsl
Windows Subsystem for Linux items I need to remember

## Password Reset if you don't remember your default user password

Basically, reset your WSL Linux distro to set your default-user to root.
Login is as root, fix your users password
Reset, default user configuration.

|Distribution| App|	Windows Command|
|------------|----|----------------|
|Ubuntu	| ubuntu | config –default-user root|
|Ubuntu 20.04|	ubuntu2004 | config –default-user root|
|Ubuntu 18.04|	ubuntu1804 | config –default-user root|
|Debian|	debian | config –default-user root|
|Kali Linux|	kali | config –default-user root|

```
ubuntu2004 config --default-user root
wsl
passwd <username>
<newpasswd>
<newpasswd>
exit
ubuntu2004 config --default-user root
wsl
```

Source:
https://itsfoss.com/reset-linux-password-wsl/

## IPv4 and Internal Proxy Issues with apt

```
# /etc/apt/apt.conf.d/ipv4onlyforinternalproxy
Acquire::ForceIPv4=true;
```

```
Acquire::http::Proxy "http://proxy.server:80/";
```

## WSL on a Laptop with VPN

- Where $VPN is your VPN vendor or implementation
- May need to run this every time you connect 

```Powershell
Get-NetAdapter | Where-Object {$_.InterfaceDescription -Match "$VPN"} | Set-NetIPInterface -InterfaceMetric 6000
```

## Podman on WSL

- [Podman.md](https://github.com/heathdbrown/wsl/blob/main/Podman.md)
- [Docker.md](https://github.com/heathdbrown/wsl/blob/main/Docker.md)
