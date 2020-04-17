
$packages = <<-SCRIPT
    yum install -y epel-release
    yum install -y htop git tig perl
    yum install -y gcc make cmake rpm-build
SCRIPT

$packagesDevel = <<-SCRIPT
    yum install -y vim
SCRIPT

$packagesSlurm = <<-SCRIPT
    yum install -y rng-tools openssl openssl-devel pam-devel numactl numactl-devel hwloc hwloc-devel lua lua-devel readline-devel rrdtool-devel ncurses-devel man2html libibmad libibumad
SCRIPT

$installMariadbAndMunge = <<-SCRIPT
    yum install -y mariadb-server mariadb-devel
    export MUNGEUSER=991
    groupadd -g $MUNGEUSER munge
    useradd  -m -c "MUNGE Uid 'N' Gid Emporium" -d /var/lib/munge -u $MUNGEUSER -g munge  -s /sbin/nologin munge
    export SLURMUSER=992
    groupadd -g $SLURMUSER slurm
    useradd  -m -c "SLURM workload manager" -d /var/lib/slurm -u $SLURMUSER -g slurm  -s /bin/bash slurm
    yum install -y munge munge-libs munge-devel
SCIRPT

$createMungeKey = <<-SCRIPT
    rngd -r /dev/urandom
    /usr/sbin/create-munge-key -r
    dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key
    chown munge: /etc/munge/munge.key
    chmod 400 /etc/munge/munge.key
SCRIPT

#TODO Copy Munge Key to other VMs
$copyMungeKey = <<-SCRIPT
    scp root@<hostname-of-server-VM>:/etc/munge/munge.key /etc/munge
    chown -R munge: /etc/munge/ /var/log/munge/
    chmod 0700 /etc/munge/ /var/log/munge/
SCRIPT

$startMunge = <<-SCRIPT
    systemctl enable munge
    systemctl start munge
SCRIPT

#TODO hardcode rpms to be installed
$installSlurm = <<-SCRIPT
    wget https://download.schedmd.com/slurm/slurm-20.02.1.tar.bz2
    rpmbuild -ta slurm-20.02.1.tar.bz2
    yum --nogpgcheck localinstall /root/rpmbuild/RPMS/x68_64/slurm-*
SCRIPT

$configureSlurmServer = <<-SCRIPT
    mkdir /var/spool/slurmctld
    chown slurm: /var/spool/slurmctld
    chmod 755 /var/spool/slurmctld
    touch /var/log/slurmctld.log
    chown slurm: /var/log/slurmctld.log
    touch /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log
    chown slurm: /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log

    systemctl enable slurmd.service
    systemctl start slurmd.service
    systemctl status slurmd.service

    systemctl enable slurmctld.service
    systemctl start slurmctld.service
    systemctl status slurmctld.service
SCRIPT

$configureSlurmWorker = <<-SCRIPT
    mkdir /var/spool/slurmd
    chown slurm: /var/spool/slurmd
    chmod 755 /var/spool/slurmd
    touch /var/log/slurmd.log
    chown slurm: /var/log/slurmd.log

    systemctl enable slurmd.service
    systemctl start slurmd.service
    systemctl status slurmd.service
SCRIPT

$disableFirewall = <<-SCRIPT
    systemctl stop firewalld
    systemctl disable firewalld
SCRIPT

#TODO
$installSpark = <<-SCRIPT
SCIRPT

$cloneMagpie = <<-SCRIPT
    git clone https://github.com/LLNL/magpie.git
SCRIPT


Vagrant.configure("2") do |config|
    config.vm.box = "centos/7"
    config.vm.hostname = "slurm-server"
#    config.vm.hostname = "slurm-worker"
    
    config.vm.provider "virtualbox" do |v|
        v.memory = 1024*4
        v.cpus = 8
    end
    
    config.vm.network "public_network"
#    config.vm.base_address "192.168.1.123"
    config.disksize.size = "20GB"
    
    config.vm.provision "shell", inline: $packages
    config.vm.provision "shell", inline: $packagesDevel
    config.vm.provision "shell", inline: $packagesSlurm

    config.vm.provision "shell", inline: $installMariadbAndMunge
    config.vm.provision "shell", inline: $createMungeKey
#    config.vm.provision "shell", inline: $copyMungeKey
    config.vm.provision "shell", inline: $startMunge
    config.vm.provision "shell", inline: $installSlurm

#   https://slurm.schedmd.com/configurator.easy.html
    config.vm.provision "file", source: "./slurm.conf", destination: "/etc/slurm/slurm.conf"
    config.vm.provision "shell", inline: $configureSlurmServer
#    config.vm.provision "shell", inline: $configureSlurmWorker
#    config.vm.provision "shell", inline: $disableFirewall

#    config.vm.provision "shell", inline: $installSpark
#    config.vm.provision "shell", inline: $cloneMagpie
end
