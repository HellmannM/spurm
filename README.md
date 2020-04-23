spark + slurm = ...

Create VM testing cluster for running spark apps inside slurm jobs.

How to use:

$ vagrant up
$ vagrant ssh [VM-NAME]

VM-NAME     HOSTNAME    ADDR
-------------------------------------
slurmvm1    slurmvm1    192.168.33.10
slurmvm2    slurmvm2    192.168.33.11

Slurm configuration was created with:
https://slurm.schedmd.com/configurator.easy.html


TODO
-enable firewall and open ports instead
-nodes are stuck in state 'drain'
