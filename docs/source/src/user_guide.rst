================================================
USER GUIDE FOR BIG DATA & BIOINFORMATICS LAB HPC
================================================

This guide is prepared to explain the steps needed to accomplish a simple analysis on the server. Please make sure you read everything carefully. If you still think you cannot handle the situation, ask for help from one of the team members (provided at the end of the document).

This guide will cover 3 sections:
1. How to connect to the server?
2. How to upload or download your files?
3. How to use SLURM to submit your job?

-----------------------------
How to connect to the server?
-----------------------------

Connection to the server is achieved by using the Terminal (Command Prompt). The connection requires users to be using ``Bezmialem`` internet. If you are not connected to ``Bezmialem``, you need to use VPN in order to access the server. VPN requires BVU username and password, which you need to ask the university to provide you. If you do not know how, ask one of the team members.

Check out the attached guide ``VPN Uzaktan Erişim Adımları`` to see how you can access, after you get your username and password. After you connect to Bezmialem or VPN, you need to open Terminal (Command Prompt) and write 

.. code-block:: bash
   :linenos:
	
	ssh username@10.100.9.30 


To learn your usernames, ask one of the team members. 

If you connect for the first time, it will ask you for your Current Password, which is 1234. Then it will ask you to change the password to something else. If you forget your password, let us know.

.. warning::
	Once you connect, you will enter your home location, which is your user director. There, you can place files, use basic Unix commands, but you should avoid running any analysis directly there. To run analysis, the only command you will use is ``sbatch``, which is explained in section 3. Do not run any tools like Python or Gromacs! **Never!**

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

1.`SSH <https://www.hostinger.com/tutorials/ssh/basic-ssh-commands>`_.

2.`SFTP <https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server>`_.


---------------------------------------
3. How to use SLURM to submit your job?
---------------------------------------

To run analysis on the HPC, we use the SLURM job scheduler. This is carried out by executing a slurm instruction Shell script file, which will be provided to each user as an ``slurm_example.sh``. The user can directly customize the codes in the file suit their analysis needs. Below is a snapshot of the codes of the example file.

.. code-block:: bash
   :linenos:

   #!/bin/bash
   #SBATCH --job-name=esra_blastdb_trial
   #SBATCH --output=esra_blastdb_trial.out
   #SBATCH --nodelist=compute1
   #SBATCH --ntasks=1
   #SBATCH --time=1:00:00
   #SBATCH --mem-per-cpu=100
   #SBATCH --ntasks-per-node=1

   srun blastp -query sequences.fasta -db HMN -out all_results_for_sequences_PAM30.txt

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

1.`Compress and extract files <https://www.tecmint.com/18-tar-command-examples-in-linux/>`_.

2.`SLURM <https://slurm.schedmd.com/>`_.

-------------------------
System Administrator Team
-------------------------

Esra Büşra Işık <ebisik@bezmialem.edu.tr> 
Faruk Üstünel <faruk.ustunel@bezmialem.edu.tr>
Muhammet Celik <mcelik@bezmialem.edu.tr>

Big Data and Bioinformatics Lab, BILSAB, BVU, Turkey.