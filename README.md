# Enabling bridge and br_netfilter modules in vanilla WSL2 (Ubuntu-22.04) to implement service-mesh in Hashcorp Nomad or Kubernets
Enable the bridge and br-netfilter modules on WSL2. This allows you to use "bridge" network mode for [Hashicorp Nomad](https://github.com/hashicorp/nomad) jobs.
I believe it also works for Kubernetes.

> These are minimal steps and/or interventions that I have tested in two different environments using WSL2 with Ubuntu 22.04 vanilla.

> All steps presented here are performed in the **WSL2 terminal** except **step 10**, which must be performed in the **Powershell terminal**.

---

### 1. Downloading the necessary requirements
```shell
# On wsl2 terminal.

sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev bc libncurses-dev unzip
```

### 2. Set variable with kernel version
####    ex: WSL_RELEASE=linux-msft-wsl-5.15.90.1
```shell
# On wsl2 terminal.

WSL_RELEASE="linux-msft-wsl-$(echo $(uname -r) | cut -d"-" -f1)"
```

### 3. Download the source code of your kernel version used by wsl2
```shell
# On wsl2 terminal.

curl -LO https://github.com/microsoft/WSL2-Linux-Kernel/archive/refs/tags/${WSL_RELEASE}.zip
```

### 4. Unzip the source code 
```shell
# On wsl2 terminal.

unzip ${WSL_RELEASE}.zip
```

### 5. Load your WSL2 kernel configuration file (Kconfig)
```shell
# On wsl2 terminal.

cd WSL2-Linux-Kernel-${WSL_RELEASE}
zcat /proc/config.gz > .config
```

### 6. Prepare the modules
```shell
# On wsl2 terminal.

make prepare modules_prepare -j $(nproc)
```

### 7. By default, the modules are already enabled in the ".config", I suggest checking them when opening the config menu
####    Modules must be enabled with "*" (built-in)
```shell
# On wsl2 terminal.

make menuconfig
```

####   Settings locations in menuconfig
>\> Networking support
>>    \> Networking options
>>>      > "[*] Network packet filtering framework (Netfilter)"
>>>      > "<*> 802.1d Ethernet Bridging"

### 8. Compile the modules
```shell
# On wsl2 terminal.

make modules -j $(nproc)
```

### 9. Activate the modules
```shell
# On wsl2 terminal.

sudo make modules_install -j $(nproc)
```

### 10. Restart wsl2:

a. In CMD or PowerShell:

```pwsh
# On Poweshel or CMD terminal.

wsl --shutdown
```
b. Reopen wsl2

---

# Have fun!

