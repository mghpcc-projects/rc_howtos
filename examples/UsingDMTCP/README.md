DMTCP is a tool that consists of saving a snapshot of the application's state, so that it can restart from that point in time. This is particularly important for long running application that are executed in a system with regular maintenance periods.

DMTCP can be used in simple computer as well as in an Heigh performance computing system. 

#To run a job with slurm on a high performance computing system :

To run your code using DMTCP, you will need your code, slurm_launch.job and slurm_restart.job ( see the DMTCP page).
 
For testing, I created a python code that will print the current time and the hostname .

the code is as follow ( saved it in a file named date.py):

    #!/usr/bin/env python
    import datetime
    import time
    import socket

    test_time = open('test.time', 'w')
    test_time.write('today is\n')
    test_time.close()
    number = 0
    for i in range(900):
       test_time = open('test.time', 'a')
       today = time.strftime("%c")
       number += 1
       time.sleep(1)
       hostname = socket.gethostname()
       print today, hostname   # 2008-03-09 <type 'datetime.date'>
       test_time.flush()
       test_time.write(str(today))
       test_time.write('\t')
       test_time.write(str(number))
       test_time.write('\t')
       test_time.write(str(hostname))
       test_time.write('\n')
       test_time.close()
    for line in file('test.time'):
         print line,


1. I added the modules needed using the commands:
   
   module add engaging/dmtcp/2.4.0 
   module add engaging/openmpi/1.8.8
   module add engaging/python/2.7.8   << because my code is a python code
   
  without the modules needed ( in may case the three above) errors will be generated.

2. In slurm_launch.job file, I  changed the slurm partition ( for my test I picked the default which run a job for 15 minutesa).

   \#SBATCH --partition=sched_any_quicktest

   or just comment out the line to pick the default slurm partition
 
   \##SBATCH --partition=sched_any_quicktest

3. In slurm_launch.job file, I  changed checkpoint time  dmtcp will take a snapshot (I edited slurm_launch.job file  and changed the line start_coordinator -i 300). 

  for info, -i will checkpoint automatically every number of second (300 seconds in my example).

4. I added the code needed to be run at the bottom of the slurm_launch.job file  (in my example, dmtcp_launch --rm mpirun ./date.py)
  
5. run the job with the command  sbatch slurm_launch.job 

  The user will end up with dmtcp_command.${SLURM_JOBID} ( In my case I got dmtcp_command.2343124 ) and the outpufile specified in slurm_launch.job file.

6. To restart the job from the last check point, run : sbatch slurm_restart.job.

