# Linux
## netplan ubuntu server
### static configuration
```
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    ethernets:
        enp0s3:
            addresses:
            - 10.0.0.1/24
            nameservers:
                addresses:
                - 10.0.0.2
                search: []
            routes:
            -   to: default
                via: 10.0.0.254
    version: 2
```

### dhcp configuration
```
network:
    ethernets:
        enp0s3:
          dhcp4: true
    version: 2
```
## usermod
```bash
usermod -aG sudo username
```

## Adding k9s o binari a PATH
```
# If you come from bash you might have to change your $PATH.
export PATH=$HOME/bin:/usr/local/bin:$PATH
export PATH=/usr/lib/python3.10:$PATH
export PATH=/snap/k9s/current/bin:$PATH
# Path to your oh-my-zsh installation.
export ZSH="$HOME/.oh-my-zsh"
```

## Homebrew installation
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
(echo; echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"') >> /home/luca/.bashrc
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
sudo apt-get install build-essential
```
