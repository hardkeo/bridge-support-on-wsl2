# Enabling bridge and br_netfilter modules in vanilla WSL2 (Ubuntu-22.04)
Enable the bridge and br-netfilter modules on WSL2. This allows you to use "bridge" network mode for [Hashicorp Nomad](https://github.com/hashicorp/nomad) jobs.
I believe it also works for Kubernetes.

> These are minimal steps and/or interventions that I have tested in two different environments using WSL2 with Ubuntu 22.04 vanilla.

---

### 1. Downloading the necessary requirements
```shell
sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev bc libncurses-dev unzip
```

### 2. Set variable with kernel version
####    ex: WSL_RELEASE=linux-msft-wsl-5.15.90.1
```shell
WSL_RELEASE="linux-msft-wsl-$(echo $(uname -r) | cut -d"-" -f1)"
```

### 3. Download the source code of your kernel version used by wsl2
```shell
curl -LO https://github.com/microsoft/WSL2-Linux-Kernel/archive/refs/tags/${WSL_RELEASE}.zip
```

### 4. Unzip the source code 
```shell
unzip ${WSL_RELEASE}.zip
```

### 5. Load your WSL2 kernel configuration file (Kconfig)
```shell
cd WSL2-Linux-Kernel-${WSL_RELEASE}
zcat /proc/config.gz > .config
```

### 6. Prepare the modules
```shell
make prepare modules_prepare -j $(nproc)
```

### 7. By default, the modules are already enabled in the ".config", I suggest checking them when opening the config menu
####    Modules must be enabled with "*" (built-in)
```shell
make menuconfig
```

####   Settings locations in menuconfig
>\> Networking support
>>    \> Networking options
>>>      > "[*] Network packet filtering framework (Netfilter)"
>>>      > "<*> 802.1d Ethernet Bridging"

### 8. Compile the modules
```shell
make modules -j $(nproc)
```

### 9. Activate the modules
```shell
sudo make modules_install -j $(nproc)
```

### 10. Restart wsl2:

a. In CMD or PowerShell:

```shell
"wsl --shutdown"
```
b. Reopen wsl2

---

# Have fun!

