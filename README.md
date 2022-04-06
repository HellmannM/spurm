spark + slurm = spurm

Create VM testing cluster for running spark apps inside slurm jobs.

## How to use:

```bash
$ vagrant up
$ vagrant ssh [VM-NAME]
```

```
VM-NAME     HOSTNAME    ADDR
-------------------------------------
slurmvm1    slurmvm1    10.0.0.10
slurmvm2    slurmvm2    10.0.0.11
```

Slurm configuration was created with [Slurm SchedMD](https://slurm.schedmd.com/configurator.easy.html).


TODO  
- enable firewall and open ports instead
- nodes are stuck in state 'drain'
- need to add network in virtualbox manually?
