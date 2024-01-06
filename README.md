# Enabling bridge and br_netfilter modules in vanilla WSL2 (Ubuntu-22.04) to implement service-mesh in Hashcorp Nomad or Kubernetes.
Enable the bridge and br-netfilter modules on WSL2. This allows you to use "bridge" network mode for [Hashicorp Nomad](https://github.com/hashicorp/nomad) jobs.
I believe it also works for Kubernetes.

> These are minimal steps that I have tested in various environments using WSL2 with Ubuntu 22.04 vanilla.

> All steps presented here are performed in the **WSL2 terminal**.

---

### 1. Create kernel module directory if it does not exist
```shell
# On wsl2 terminal.

sudo mkdir -p /lib/modules/$(uname -r)
```

### 2. Create modules.builtin file in to kernel module directory
```shell
# On wsl2 terminal.

sudo tee /lib/modules/$(uname -r)/modules.builtin <<-EOF
kernel/net/bridge/bridge.ko
kernel/net/bridge/br_netfilter.ko
EOF
```

### 3. Restart wsl2:
```shell
# On wsl2 terminal.

wsl.exe --shutdown
```
Reopen wsl2

---
# Have fun!

