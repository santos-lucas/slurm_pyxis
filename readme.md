# pyxis_slurm


## Description

This is provides the installation of the pyxis plugin on top of your your slurm environment.

## Instructions

### Prerequisites

Before you enable the pyxis plugin on your environment, you need to make sure you have [enroot](https://github.com/NVIDIA/enroot) and [pyxis](https://github.com/NVIDIA/pyxis) packages on you repositories, so make sure you follow their instructions and build the packages for ther architecutires / OS versions you need.

This role expects your slurm prolog and epilog to be already configured and to run all scripts under /etc/slurm/prolog.d/ and /etc/slurm/epilog.d/ respictively on prolog and epilog process.

You could also use this [prolog_epilog](https://github.com/santos-lucas/bluebanquise-lss-custom/tree/main/roles/prolog_epilog) role also to configure very basic prolog and epilog scripts that will be compatible with this role.

### Slurm profile
As the configuration will be different on an slurm compute node or in a login/management node, you shoulde use the variable slurm_profile to controle the role behavior, set it for `compute`,`submitter` or `controller`.

### Add the pyxis_slurm role to your playbook

Once you have the repositories setted up, you just need to make sure you add the pyxis_slurm role on you playbook after the slurm role:

```yaml
- name: Management playbook 
  hosts: mg_managements
  vars:
    slurm_profile: controller

  roles:
    - role: slurm
      tags: slurm
    - role: pyxis_slurm
      tags: pyxis_slurm
```

 For a compute node:

 ```yaml
- name: Management playbook 
  hosts: mg_managements
  vars:
    slurm_profile: compute

  roles:
    - role: slurm
      tags: slurm
    - role: pyxis_slurm
      tags: pyxis_slurm
```



## Considerations

### Nvidia GPUs

To enable support for nvidia gpus, just change the inventory to make sure you have the variable "pyxis_enable_libnvidia_container" set to True (it is set to false by default).

```yaml
pyxis_enable_libnvidia_container: true
```

Make sure you have the nvidia-container-toolkit repo on your environment. You should be able to get the online repo configuration from [this link](https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo). We strongly recommend you to clone it and have it available locally to ensure all servers will have the same versions, regardless of when you installed it on each server.

### Enroot configuration

For now, this role uses this enroot [configuration example](https://github.com/NVIDIA/pyxis/wiki/Setup#enroot-configuration-example) with some minor changes change:

```
ENROOT_RUNTIME_PATH /run/enroot/user-$(id -u)
ENROOT_CACHE_PATH /tmp/enroot-cache/user-$(id -u)
ENROOT_DATA_PATH /tmp/enroot-data/user-$(id -u)
ENROOT_SQUASH_OPTIONS -noI -noD -noF -noX -no-duplicates
ENROOT_MOUNT_HOME y 
ENROOT_RESTRICT_DEV y
ENROOT_ROOTFS_WRITABLE y
```

we are using this minor change for dwfault because it will be normally needed by HPC, as the user execution environment can be placed on his home directory.

for this initial version, those configurations are not changeble thorugh ansible variables, but feel free to change the [template](templates/enroot.conf.j2) if needed.


##### TODO:
 - Add limit size for cleaning enroot cache on the epilog  

## Changelog
* 1.1.1: Add slurm_profile var to match target installation. Lucas Santos <lucassouzasantos@gmail.com>
* 1.1.0: Add libnvidia-container installation support. Lucas Santos <lucassouzasantos@gmail.com>
* 1.0.0: Role creation. Lucas Santos <lucassouzasantos@gmail.com>
