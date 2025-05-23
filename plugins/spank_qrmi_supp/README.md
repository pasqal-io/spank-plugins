# Supplemental SPANK Plugin for QRMI

This is [SPANK plugin](https://slurm.schedmd.com/spank.html) to set supplemental environment variables which can not be covered by [spank_qrmi](../spank_qrmi/README.md).
 
## Prerequisites

* CMake
* gcc
* Slurm header & library
  * slurm/slurm.h must be under /usr/include
  * libslurm.so must be under /usr/lib64/slurm or /usr/lib/x86_64-linux-gnu/slurm

## How to build

```shell-session
mkdir build
cd build
cmake ..
make
```

## Installation

If the above build step is successful, a Linux shared library named `libspank_qrmi_supp.so` will be created under the `build/` directory. 

SPANK plugin are loaded in up to five separate contexts during a Slurm job as described in [this page](https://slurm.schedmd.com/spank.html#SECTION_SPANK-PLUGINS). Copy this library to `/usr/lib64/slurm` directory on the nodes load this plugin.

In addition, add the following 1 line to the /etc/slurm/plugstack.conf on the nodes where this plugin is installed.

```bash
optional /usr/lib64/slurm/libspank_qrmi_supp.so
```

> [!NOTE]
> This plugin refers '--qpu=names' option which is registered by [spank_qrmi](../spank_qrmi/README.md). This `spank_qrmi` plugin must be listed in plugstack.conf.

## Logging

This plugin uses Slurm logger for logging. Log messages from this plugin can be found in /var/log/slurm/slurmd.log, etc.

```bash
[2025-05-06T02:48:52.034] [81.batch] debug:  spank: /etc/slurm/plugstack.conf:2: Loaded plugin libspank_qrmi_supp.so
[2025-05-06T02:48:52.089] [81.batch] debug:  spank_qrmi_supp(1989, 1001): -> slurm_spank_task_init argc=0 remote=1
[2025-05-06T02:48:52.092] [81.batch] debug2: spank: libspank_qrmi_supp.so: task_init = 0
[2025-05-06T02:48:52.134] [81.0] debug:  spank: /etc/slurm/plugstack.conf:2: Loaded plugin libspank_qrmi_supp.so
[2025-05-06T02:48:52.142] [81.0] debug:  spank_qrmi_supp(2016, 1001): -> slurm_spank_task_init argc=0 remote=1
[2025-05-06T02:48:52.144] [81.0] debug2: spank: libspank_qrmi_supp.so: task_init = 0
```

## License

[GPL-3.0](https://github.com/qiskit-community/spank-plugins/blob/main/LICENSE)
