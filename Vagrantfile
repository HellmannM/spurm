
$packages = <<-SCRIPT
    yum install -y epel-release
    yum install -y htop git tig perl curl wget
    yum install -y gcc make cmake rpm-build
SCRIPT

$packagesDevel = <<-SCRIPT
    yum install -y vim
SCRIPT

$packagesSlurm = <<-SCRIPT
    yum install -y python3 perl-ExtUtils-MakeMaker perl-Switch rng-tools openssl openssl-devel pam-devel numactl numactl-devel hwloc hwloc-devel lua lua-devel readline-devel rrdtool-devel ncurses-devel man2html libibmad libibumad
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
SCRIPT

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

$installSlurm = <<-SCRIPT
    wget -q https://download.schedmd.com/slurm/slurm-20.02.1.tar.bz2
    rpmbuild -ta slurm-20.02.1.tar.bz2
    yum --nogpgcheck localinstall -y \
        /root/rpmbuild/RPMS/x86_64/slurm-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-perlapi-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-devel-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-example-configs-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-slurmctld-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-slurmd-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-slurmdbd-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-libpmi-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-torque-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-openlava-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-contribs-20.02.1-1.el7.x86_64.rpm \
        /root/rpmbuild/RPMS/x86_64/slurm-pam_slurm-20.02.1-1.el7.x86_64.rpm
SCRIPT

$configureSlurmServer = <<-SCRIPT
    mkdir -p /var/spool/slurmctld
    chown slurm: /var/spool/slurmctld
    chmod 755 /var/spool/slurmctld
    touch /var/log/slurmctld.log
    chown slurm: /var/log/slurmctld.log
    touch /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log
    chown slurm: /var/log/slurm_jobacct.log /var/log/slurm_jobcomp.log

    systemctl enable slurmctld.service
    systemctl start slurmctld.service
    systemctl status slurmctld.service
SCRIPT

$configureSlurmWorker = <<-SCRIPT
    mkdir -p /var/spool/slurmd
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

$installJava = <<-SCRIPT
    yum install -y java-1.8.0-openjdk
    echo 'export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk' | sudo tee -a /etc/profile
    echo 'export JRE_HOME=/usr/lib/jvm/jre' | sudo tee -a /etc/profile
    source /etc/profile
SCRIPT

$installScala = <<-SCRIPT
    wget -q https://downloads.lightbend.com/scala/2.13.1/scala-2.13.1.rpm
    yum localinstall -y scala-2.13.1.rpm
SCRIPT

#TODO
$installSpark = <<-SCRIPT

	curl -O https://archive.apache.org/dist/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz
	tar xvf spark-2.4.4-bin-hadoop2.7.tgz
	sudo mv spark-2.4.4-bin-hadoop2.7/ /opt/spark
	echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc
	echo 'export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin' >> ~/.bashrc
	source ~/.bashrc

SCRIPT

$cloneMagpie = <<-SCRIPT
    git clone https://github.com/LLNL/magpie.git
SCRIPT


Vagrant.configure("2") do |config|
    config.vm.define "node1" do |vm1|
	    vm1.vm.box = "centos/7"
	    vm1.vm.box_version = "1905.1"
	    vm1.vm.hostname = "slurmvm1"
	    vm1.vm.network "private_network", ip: "192.168.33.10"
	    
	    vm1.vm.provider "virtualbox" do |v|
		v.memory = 1024
		v.cpus = 2
	    end
	    
	#TODO
	#   vm1.vm.network "public_network", ip: "..."
	#   vm1.vm.base_address "192.168.1.123"
	    
	    vm1.vm.provision "shell", inline: $packages
	    vm1.vm.provision "shell", inline: $packagesDevel
	    vm1.vm.provision "shell", inline: $packagesSlurm

	    vm1.vm.provision "shell", inline: $installMariadbAndMunge
	    vm1.vm.provision "shell", inline: $createMungeKey
	#   vm1.vm.provision "shell", inline: $copyMungeKey
	    vm1.vm.provision "shell", inline: $startMunge
	    vm1.vm.provision "shell", inline: $installSlurm

	#   https://slurm.schedmd.com/configurator.easy.html
	    vm1.vm.provision "file", source: "./slurm.conf", destination: "/tmp/slurm.conf"
	    vm1.vm.provision "shell", inline: "mkdir -p /etc/slurm && cp /tmp/slurm.conf /etc/slurm/slurm.conf"
	    vm1.vm.provision "shell", inline: $configureSlurmServer
	#   vm1.vm.provision "shell", inline: $configureSlurmWorker
	#   vm1.vm.provision "shell", inline: $disableFirewall

	#TODO add key first?
	#   vm1.vm.provision "shell", inline: $cloneMagpie

	    vm1.vm.provision "shell", inline: $installJava
	    vm1.vm.provision "shell", inline: $installScala
	    vm1.vm.provision "shell", inline: $installSpark
	end

	config.vm.define "node2" do |vm2|
	    vm2.vm.box = "centos/7"
	    vm2.vm.box_version = "1905.1"
	    vm2.vm.hostname = "slurmvm2"
	    vm2.vm.network "private_network", ip: "192.168.33.20"
	    
	    vm2.vm.provider "virtualbox" do |v|
		v.memory = 1024
		v.cpus = 2
	    end
	    
	#TODO
	#   vm2.vm.network "public_network", ip: "..."
	#   vm2.vm.base_address "192.168.1.123"
	    
	    vm2.vm.provision "shell", inline: $packages
	    vm2.vm.provision "shell", inline: $packagesDevel
	    vm2.vm.provision "shell", inline: $packagesSlurm

	    vm2.vm.provision "shell", inline: $installMariadbAndMunge
	    vm2.vm.provision "shell", inline: $createMungeKey
	#   vm2.vm.provision "shell", inline: $copyMungeKey
	    vm2.vm.provision "shell", inline: $startMunge
	    vm2.vm.provision "shell", inline: $installSlurm

	#   https://slurm.schedmd.com/configurator.easy.html
	    vm2.vm.provision "file", source: "./slurm.conf", destination: "/tmp/slurm.conf"
	    vm2.vm.provision "shell", inline: "mkdir -p /etc/slurm && cp /tmp/slurm.conf /etc/slurm/slurm.conf"
	#   vm2.vm.provision "shell", inline: $configureSlurmServer
	    vm2.vm.provision "shell", inline: $configureSlurmWorker
	#   vm2.vm.provision "shell", inline: $disableFirewall

	#TODO add key first?
	#   vm2.vm.provision "shell", inline: $cloneMagpie

	    vm2.vm.provision "shell", inline: $installJava
	    vm2.vm.provision "shell", inline: $installScala
	    vm2.vm.provision "shell", inline: $installSpark
	end

end
