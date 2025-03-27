# pyxis_slurm


## Description

This is provides the installation of the pyxis plugin on top of your your slurm environment.

## Instructions

### Prerequisites

Before you enable the pyxis plugin on your environment, you need to make sure you have [enroot](https://github.com/NVIDIA/enroot) and [pyxis](https://github.com/NVIDIA/pyxis) packages on you repositories, so make sure you follow their instructions and build the packages for ther architecutires / OS versions you need.

This role expects your slurm prolog and epilog to be already configured and to run all scripts under /etc/slurm/prolog.d/ and /etc/slurm/epilog.d/ respictively on prolog and epilog process.

### Add the pyxis_slurm role to your playbook

Once you have the repositories setted up, you just need to make sure you add the pyxis_slurm role on you playbook after the slurm role

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

## Considerations

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


# TODO:
# - Add limit size for cleaning enroot cache on the epilog  

## Changelog

* 1.0.0: Role creation. Lucas Santos <lucassouzasantos@gmail.com>
