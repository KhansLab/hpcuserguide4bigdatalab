=================================================
User Guide for HPC in Big Data&Bioinformatics Lab 
=================================================

This guide is prepared to explain the steps needed to accomplish a simple analysis on the server. Please make sure you read everything carefully. If you still think you cannot handle the situation, ask for help from one of the team members (provided at the end of the document).

This guide will cover 3 sections:

1. How to connect to the server?

2. How to upload or download your files?

3. How to use SLURM to submit your job?

-----------------------------
How to connect to the server?
-----------------------------

Connection to the server is achieved by using the Terminal (Command Prompt). The connection requires users to be using ``Bezmialem`` internet. If you are not connected to ``Bezmialem``, you need to use VPN in order to access the server. VPN requires BVU username and password, which you need to ask the university to provide you. If you do not know how, ask one of the team members.

Ask one of the team members for the ``VPN Uzaktan Erişim Adımları`` guide to see how you can access, after you get your username and password. After you connect to Bezmialem or VPN, you need to open Terminal (Command Prompt) and write 

.. code-block:: bash
   :linenos:
	
   ssh username@10.100.9.30 


To learn your usernames, ask one of the team members. 

If you connect for the first time, it will ask you for your Current Password, which is 1234. Then it will ask you to change the password to something else. If you forget your password, let us know.

.. warning::
	Once you connect, you will enter your home location, which is your user directory. There, you can place files, use basic Unix commands, but you should avoid running any analysis directly there. To run analysis, the only command you will use is ``sbatch``, which is explained in section 1.3. Do not run any tools like Python or Gromacs! **Never!**

You can check this useful link to learn how to use servers, in general `from here <https://datascienceguide.github.io/beginner-tutorial-how-to-get-started-with-data-science-using-servers>`_.

-------------------------------------
How to upload or download your files?
-------------------------------------

There are two ways to connect to the server. If you are going to do analysis, you need to connect by typing:

.. code-block:: bash
   :linenos:

   ssh username@10.100.9.30


If you want to upload or download files, you need to connect by typing:

.. code-block:: bash
   :linenos:
   
   sftp username@10.100.9.30


After you access the server with ``sftp``, you can check the existing files inside your folder on the server by typing ``ls``. If you want to upload a file from your local computer, you can check your files by typing ``lls``. So, the main idea is, you are controlling to two different computers. One is your own computer, the other one is the server. ``cd`` command is used to change the directory. If you use it, you will change directories at the server. When your type ``lcd`` you will change the directory on your local computer. The difference is the letter ``l``, which denotes ``local``. Below we show how to actually do the transfer.

You need to find the file you want to upload on your computer, then type 

.. code-block:: bash
   :linenos:

   put name_of_the_file


If you want to download a file from the server, you need to type

.. code-block:: bash
   :linenos:
   
   get name_of_the_file 


Here are some useful links to understand ``ssh`` and ``sftp`` commands:

`SSH <https://www.hostinger.com/tutorials/ssh/basic-ssh-commands>`_.

`SFTP <https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server>`_.


------------------------------------
How to use SLURM to submit your job?
------------------------------------

To run analysis on the HPC, we use the SLURM job scheduler. This is carried out by executing a slurm instruction Shell script file, which will be provided to each user as an ``slurm_example.sh``. The user can directly customize the codes in the file suit their analysis needs. Below is a snapshot of the codes of the example file.

General Note: Do take note that the Slurm (job scheduler) scripts below are just an allocation instruction to the server, and your job may not take full advantage of the allocation if the settings of your tool are not aligned or in agreement with the server allocation through Slurm.

.. code-block:: bash
   :linenos:

   #If your tool does not utilize multithreading, such as muscle / or your own python script, use below.
   #Please do not change the value for "--nodes"
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   
   srun command
   
   #Template: 2
   #If your tool utilizes multithreading, such as CD-HIT and STAR aligner, among others.
   #Please do not change the value for the "--nodes" and the "--cpus-per-task".
   #You may change the time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --cpus-per-task=20
   #SBATCH -t 23:00:00 #time limit
   
   srun command
   
   #Template: 3
   #If your tool utilizes multiprocessing without multithreading
   #Please do not change the value for the "--nodes" and the "--ntasks"
   #You may change the time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --ntasks=20
   #SBATCH --time=2-00:00:00 #time limit
   
   srun command
   
   #Template: 4
   #If your tool utilizes multiprocessing along with multithreading, you can adapt the params as long as
   #The value for "--ntasks" multipled (*) with "--cpus-per-task" is not greater than 20.
   #You may change the time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --cpus-per-task=4
   #SBATCH --ntasks=5
   #SBATCH --time=2-00:00:00 #time limit
   
   srun command
   
   #Template: 5
   #This is for GPU usage.
   #The values for "--nodes" and "--cpus-per-gpu" should not be changed.
   #You may change the time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --cpus-per-gpu=2
   #SBATCH --gres=gpu:1
   #SBATCH --time=2-00:00:00 #time limit
   
   srun command
   
   #Template: 6
   #This is for GPU usage.
   #If your tool is able to utilize multiprocessing on a single GPU. 
   #You can change the "--ntasks" as per the guideline of your tool. Ensure that there is no conflict between the #internal settings of the tool and the instruction sent to    Slurm.   
   #Do th   is with care as you might overload the #GPU and your job will be terminated.   
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --cpus-per-task=1
   #SBATCH --ntasks=20
   #SBATCH --gres=gpu:1
   #SBATCH --time=2-00:00:00 #time limit
   
   srun command
   
   #Template: 7
   #This is for GPU usage.
   #If your tool is able to do multiprocessing and you want to utilize GPUs on the two nodes (GPU 1 and GPU 2).
   #Make sure your tool can run on two GPUs on different nodes.  
   #Do not change any of the settings below, except time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=2
   #SBATCH --cpus-per-gpu=3
   #SBATCH --gpus-per-node=1
   #SBATCH --time=2-00:00:00 #time limit
   
   srun command
   
   #Template: 8
   #Needs Permission! 
   #If you need the whole node with multithreading.
   #Do not change any of the settings below, except time limit.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --ntasks-per-node=1
   #SBATCH --cpus-per-task=100
   #SBATCH --time=2-00:00:00 #time limit
   
   #Template: 9
   #Needs Permission! 
   #If you need two nodes with multiprocessing. 
   #If one node is enough, just adjust --nodes=2 to --nodes=1
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=2
   #SBATCH --ntasks-per-node=100
   #SBATCH --time=2-00:00:00 #time limit
   
   #Template: 10
   #Needs Permission! 
   #If you need two nodes with high cpu power (multiprocessing coupled with multithreading), use below.
   #NOTE: if your tool is not capable of doing multiprocessing, this won't work. Then you can only go with 1 #node.
   #Multithreading != multiprocessing. For example: "muscle -super5" is able to do multithreading, but not #multiprocessing.
   #In contrast, MAGUS is able to do both multiprocessing and multithreading. 
   #In bioinformatics, it is rare to find multiprocessing tools.
   #You may modify the settings to better maximise your tool's capability, but do it with care.
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=2
   #SBATCH --ntasks-per-node=1
   #SBATCH --cpus-per-task=100
   #SBATCH --time=2-00:00:00 #time limit
   
   #Template: 11
   #This is for GROMACS (on GPU node)
   #Since you are going to use the GROMACS tool, please add an additional line to call #the GROMACS software that #has been installed on the server for global use (any user). 
   #The line to add is: "source /home/software/gromacsGPU/bin/GMXRC" (without quotes) before srun.
   #You can adjust "--cpus-per-task", but do it with care. 
   #You may also change the time limit. 
   
   #!/bin/bash
   #SBATCH --job-name=<NameOfYourJob>
   #SBATCH --output=<StdoutOfYourJob>
   #SBATCH --nodes=1
   #SBATCH --cpus-per-task=2
   #SBATCH --gres=gpu:1
   #SBATCH --time=2-00:00:00 #time limit
   
   source /home/software/gromacsGPU/bin/GMXRC
   
   srun gmx_mpi grompp -f mdPL.mdp -c npt_prsa_cat.gro -t npt_prsa_cat.cpt -p topol.top -n index_prsa_cat.ndx -o md.tpr -maxwarn 3
   
   srun gmx_mpi mdrun -s md.tpr -deffnm md_prsa_cat2 -nb gpu


^^^^^^^^^^^^^^^^^^^^^^^^^^^
About the “#SBATCH” section
^^^^^^^^^^^^^^^^^^^^^^^^^^^

You should not change this section of the file as much as possible. However, the following are likely candidates for change:
- ``--job-name`` – which is the name of your job
- ``--output``   – which is the name of your output file of the STDOUT

As mentioned, the other parts should remain the same as much as possible, unless there are exceptions, for which see the important notes below.

.. warning:: 
	If you are going to be utilizing GPU, then change: ``--nodelist=compute1`` to ``--nodelist=gpu1``.
	
.. warning::
	The ``--time`` indicates the time your analysis will be allowed to run. If your analysis will take shorter than that, then it is not a problem. However, if your job will take longer, you can adjust accordingly. In case the job might take longer than 12 hours, you must inform one of the administrators, otherwise risk it from being terminated. As it can be difficult to determine how long the job might take, one could do an estimation by running the job on a smaller subset of the data and extrapolate from there.

^^^^^^^^^^^^^^^^^^^^^^
About the srun section
^^^^^^^^^^^^^^^^^^^^^^

This is where you will write your analysis code. Make sure you write the code after the ``srun`` command.

.. note:: 

	Please ensure that all input files to be analysed are within your user folder. Also, do note that all output files to be produced are stored only within your user folder location. In the given example, the input file named ``sequences.fasta`` is inside the user folder ``Esra``.  Also, all the files relevant to the database ``HMN`` are also in the same user folder. The location of the output file ``all_results_for_sequences_PAM30.txt`` is also indicated to be produced within the same folder. 
	
	Do note that the location of the earlier output file ``esra_blastdb_trial.out`` for the ``#SBATCH`` section is also to be stored in the user folder.

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Executing the slurm instruction shell script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Assuming you have uploaded your files to your home directory ``user folder``, including the ``slurm_example.sh`` file, you will run in terminal:

.. code-block:: bash
   :linenos:
	
   sbatch slurm_example.sh

and your analysis will start. You can check the status of your analysis by writing ``squeue``.

When you are done with everything, you can type

.. code-block:: bash
   :linenos:

   exit 

and disconnect from the server.


That’s it!

There are many resources on the web, do not be shy to check them out and learn more.

For example:

`Compress and extract files <https://www.tecmint.com/18-tar-command-examples-in-linux/>`_.

`SLURM <https://slurm.schedmd.com/>`_.


--------------------------------------
How to use PBS PRO to submit your job?
--------------------------------------

PBS Pro is a workload management system that allows users to submit and manage jobs on a cluster or supercomputer. Here are the basic steps to use PBS Pro for job submission:

^^^^^^^^^^^^^^^^^
Connect to server
^^^^^^^^^^^^^^^^^

First, you need to connect to the server using SSH. You will need the IP address of the server which is 10.100.9.30, as well as your username and password to log in.

^^^^^^^^^^^^^^^^^^^^^^^
Prepare your job script
^^^^^^^^^^^^^^^^^^^^^^^

Create a script file that contains the commands and parameters that you want to run on the cluster. The script should start with the shebang line (#!/bin/bash or #!/bin/sh) and any necessary environment variables or module loads. After that, you should specify the PBS directives that define the resource requirements for your job, such as the number of nodes, processors, memory, runtime, etc. Finally, you should include the actual commands that you want to run in the script.

Here is an example of job script.

.. code-block:: bash
   :linenos:

   #!/bin/bash

   #PBS -N my_job_name
   #PBS -l nodes=2:ppn=4
   #PBS -l walltime=00:10:00
   #PBS -o /path/to/stdout
   #PBS -e /path/to/stderr
   #PBS -q my_queue

   # Load any necessary modules
   module load my_module

   # Change to the directory where the job will be executed
   cd /path/to/working/directory

   # Run the command(s) you want to execute
   ./my_program input_file output_file

   # End of job script


In this example, the first line (#!/bin/bash) is a shebang line that specifies the shell that will be used to run the commands in the script.

The PBS directives start with #PBS and specify various job parameters, such as the job name (-N), the number of nodes and processors required (-l nodes=2:ppn=4), the maximum wall time the job can run for (-l walltime=00:10:00), the path to the standard output (-o /path/to/stdout), the path to the standard error (-e /path/to/stderr), and the queue name (-q my_queue).

After the PBS directives, any necessary modules are loaded using the module load command. Then the working directory is changed using the cd command to the directory where the job will be executed.

Finally, the command to be executed is specified using the ./my_program input_file output_file command. This command will run my_program with input_file as input and output_file as output.

Once all of the necessary commands have been specified in the job_script.sh file, it can be submitted to the server.

.. important::
   1. Each user is able to submit jobs and utilize 32 CPUs maximum.

   2. The queues are divided to 3 categories:

      - ShortQ: Highest Priority, maximum 4 hours walltime
      - MedQ: Medium Priority, maximum 48 hours walltime
      - LongQ: Lowest Priority, maximum 336 hrs walltime

   If you do not specify walltime, it will go to ShortQ.


^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Submit your job to the queue
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To submit your job to the queue, you need to use the qsub command followed by the name of your job script:

.. code-block:: bash
   :linenos:

   qsub job_script.sh

^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Check the status of your job
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can check the status of your job using the qstat command:

.. code-block:: bash
   :linenos:

   qstat

=========================================================
User Guide for Workstation in Big Data&Bioinformatics Lab 
=========================================================


---------------------------------
How to access the desktop server?
---------------------------------


1. The login password to access the desktop is ``1988``. 

2. To access and use the tools and libraries installed on WSL (Windows Subsystem for Linux), one can:

 2.1. Click on the search tab, type Command Prompt and type ``wsl``.
 2.2. Click run (start symbol + R), type ``wsl`` and run.

3. Wsl terminal will prompt the default user which is the ``guest``. The first thing to do after running the wsl is to change the user to your account. Everyone in the lab has their own username. If it is not, inform the Administrator Team. To change to your user account, type:

.. code-block:: bash
   :linenos:
   
   su - <your username>

You will be prompted to enter a password (default password is 1234). If you have not changed the default password for your account yet, once you have logged into your account, please change your password by typing the following and follow the instructions:

.. code-block:: bash
   :linenos:

   passwd

4. From your account, you can use the tools that are already installed on the WSL. To see the list of tools, see section 2.

5. To save files from WSL to the computer (you should save it to your folder in D), type;

.. code-block:: bash
   :linenos:

   cp "filename" /mnt/d/"username"

6. For remote access to the desktop server, you can use Microsoft Remote Desktop (both Windows and Mac). You need IP address, username, and password in order to access to the computer. For these information, please inform one of the team members.

--------------------
Some rules to follow
--------------------

1. Please do not download anything under C:\Windows. If the Windows directory gets corrupted, we will need to format the whole system and set up everything again, which is going to be a lot of work and may disrupt the work of others. 

2. The downloaded files will be located at ``C:\Users\bvukh\Downloads`` if you use chrome. After downloading your file, please move it to the drive that has been designated as a user storage (D:). The ``C:\Users\bvukh\Downloads`` will be cleared from time to time. So, make sure you have moved your file to your user folder in the D drive.

3. Most tools and libraries are already installed. When you want something to be installed, please check the ``installed_libs.txt`` and ``installed tools on wsl`` files, which can be found on the desktop. In case the tool or library you want is not installed, you can let one of the team members know.

4. Please clear the ``Recycle Bin`` when you are done.

5. Please do not save your files on the desktop. They will be deleted if found.

---------------
Recommendations
---------------

1. Some websites can’t be reached through the Bezmialem internet. In such a case, let one of the team members know.

2. Please aim to fully utilise the threads/cores of the CPU within your allocation. You can check the status of the threads by typing the following to the command line:

.. code-block:: bash
   :linenos:
    
   htop

.. warning::

   Do bear in mind that the jobs of others may be running. If you plan to run a multi-core job, please discuss with the admin team. This is to ensure that your workflow will not disrupt or kill the job of others. Some jobs may have been running for days/weeks/months, so it is important that your submission will not directly or indirectly affect the work of others. If unsure, always check with the Admin team.

3. Please be courteous to the needs of others in terms of running your job to the server. We hope to implement a better job management system in the near future. Let’s be courteous to the needs of others and try to manage this on an ad-hoc basis for now.


---------------
FAQs
---------------


Q1. What to do to find out if your tool supports multi-threading (same CPU)?

A1. Take template 4, make a small test dataset, and change "--cpus-per-task" to "20" and "ntasks" to "1". Take note of the runtime. Then repeat with the same dataset with the default template 4, as below. You may need to adapt the parameters within the tool. Sometimes it is automatic and sometime, no. If you notice that your job was completed faster with the higher "cpus-per-task" then it means your tool supports multi-threading. 

#!/bin/bash
#SBATCH --job-name=<NameOfYourJob>
#SBATCH --output=<StdoutOfYourJob>
#SBATCH --nodes=1
#SBATCH --cpus-per-task=4
#SBATCH --ntasks=5
#SBATCH --time=2-00:00:00 #time limit

Q2. What to do to find out if your tool supports multi-processing (different CPUs, say on different nodes)? 

A2. It would be easier if you check the documentation of the tool or ask the author of the tool. 


=========================
System Administrator Team
=========================

Esra Büşra Işık <ebisik@bezmialem.edu.tr> 

Faruk Üstünel <faruk.ustunel@bezmialem.edu.tr>

Muhammet Celik <mcelik@bezmialem.edu.tr>

Big Data and Bioinformatics Lab, BILSAB, BVU, Turkey.