spark + slurm = ...

Create VM testing cluster for running spark apps inside slurm jobs.

The address of first VM is 198.168.33.10 and second VM is 192.168.33.10

TODO - Kindly update the .bashrc files in both the VMs for running spark                                
echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc            
echo 'export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin' >> ~/.bashrc                              
source ~/.bashrc
