spark + slurm = ...

Create VM testing cluster for running spark apps inside slurm jobs.

The address of first VM is 198.168.33.10 and second VM is 192.168.33.10

TODO - Even though spark is installed on the first node, due to some issue, in the second node this is not installed. 

TODO - Kindly update the .bashrc files in both the VMs for running spark                                
echo 'export SPARK_HOME=/opt/spark' >> ~/.bashrc            
echo 'export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin' >> ~/.bashrc                              
source ~/.bashrc
