# Kali Linux on WSL notes

```bash
wsl --install -d kali-linux
```

*Installs fine but may encounter the following when updating and upgrading the first time.

* Invalid Signature
- ```bash
wget http://http.kali.org/kali/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb && sudo apt install ./kali-archive-keyring*.deb
```

* sudo: account validation failure, is your account locked?
```bash
wsl -d kali-linux -u root
```

* Missing library for perl
- W: Failed to fetch http://http.kali.org/kali/dists/kali-rolling/InRelease  The following signatures were invalid: EXPKEYSIG ED444FF07D8D0BF6 Kali Linux Repository <devel@kali.org>
- ```bash
cd /tmp/
dpkg-deb -x libcrypt1_1%3a4.4.28-2_amd64.deb .
cp -av lib/x86_64-linux-gnu/* /lib/x86_64-linux-gnu/
apt -y --fix-broken install
apt upgrade -y
```