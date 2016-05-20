---
layout: lesson
root: .
title: HTCondor scripts  
---
<div class="objectives" markdown="1">

#### Objectives
*   Learn how to submit HTCondor Jobs.   
*   Learn how to monitor the running Jobs.    
</div>

<h2>Overview</h2> 
In this section, we will learn the basics of HTCondor in submitting and monitoring jobs. The jobs are 
submitted through the login node of OSG Connect. The submitted jobs are executed on the remote worker 
node(s) and the outputs are transfered back to the login node. In the HTCondor job submit file, we have 
to describe how to execute the program and transfer the output data. 

<h2>Login to OSG Connect </h2>

First, we log in to OSG Connect:
{:class="in"}

~~~
$ ssh username@login.osgconnect.net  # username is your username
$ passwd:                            # enter your password
~~~

We will get the relevant example files using the *tutorial* command. Run the quickstart tutorial:

~~~
$ tutorial quickstart #creates a directory "tutorial-quickstart".
$ cd ~/tutorial-quickstart #relevant script and input files are inside this directory
~~~

We will look at two files in detail: "short.sh" and "tutorial01"

##Job execution script##
Inside the tutorial directory that you created or installed previously, create a test script to 
execute as your job:

~~~
$ nano short.sh
~~~

~~~
#!/bin/bash
# short.sh: a short discovery job
printf "Start time: "; /bin/date
printf "Job is running on node: "; /bin/hostname
printf "Job running as user: "; /usr/bin/id
printf "Job is running in directory: "; /bin/pwd
echo
echo "Working hard..."
sleep ${1-15}
echo "Science complete!"
~~~

To close nano, hold down Ctrl and press X. Press Y to save, and then Enter
Now, make the script executable.

~~~
$ chmod +x short.sh 
~~~

Since we used the tutorial command, all files are already in your workspace. Run 
the job locally when setting up a new job type, it is important to test your 
job outside of HTCondor before submitting into the Open Science Grid. 

~~~
$ ./short.sh
~~~

~~~
Start time: Wed Aug 21 09:21:35 CDT 2013

Job is running on node: login01.osgconnect.net

Job running as user: uid=54161(username) gid=1000(users) groups=1000(users),0(root),1001(osg-connect),1002(osg-staff),1003(osg-connect-test),9948(staff),19012(osgconnect)

Job is running in directory: /home/username/tutorial-quickstart

Working hard...

Science complete!

~~~

##Job submission file##
Create an HTCondor submit file. So far, so good! Next we will create a 
simple (if verbose) HTCondor submit file.

~~~
$ nano tutorial01
~~~

~~~
# The UNIVERSE defines an execution environment. You will almost always use VANILLA. 
Universe = vanilla 

# EXECUTABLE is the program your job will run It's often useful 
# to create a shell script to "wrap" your actual work. 
Executable = short.sh 

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
Error = job.error
Output = job.output

# The LOG file is where HTCondor places information about your 
# job's status, success, and resource consumption. 
Log = job.log

# QUEUE is the "start button" - it launches any jobs that have been 
# specified thus far. 
Queue 1
~~~

##Job submission##
Submit the job using condor_submit.

~~~
$ condor_submit tutorial01
Submitting job(s). 
1 job(s) submitted to cluster 823.
~~~

##Job status##

The condor_q command tells the status of currently running jobs. Generally you will want to limit it to your own jobs:

~~~
$ condor_q username
-- Submitter: login01.osgconnect.net : <128.135.158.173:43606> : login01.osgconnect.net
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
 823.0   username           8/21 09:46   0+00:00:06 R  0   0.0  short.sh
1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended
~~~

If you want to see all jobs running on the system, use condor_q without any extra parameters.
You can also get status on a specific job cluster:

~~~
$ condor_q 823
-- Submitter: login01.osgconnect.net : <128.135.158.173:43606> : login01.osgconnect.net
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
 823.0   username           8/21 09:46   0+00:00:10 R  0   0.0  short.sh
1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended
~~~

Note the ST (state) column. Your job will be in the I state (idle) if it hasn't 
started yet. If it's currently scheduled and running, it will have state R (running). If it has completed already, it will not appear in condor_q.

Let's wait for your job to finish – that is, for condor_q not to show the job in its output. A useful tool for this is watch – it runs a program repeatedly, letting you see how the output differs at fixed time intervals. Let's submit the job again, and watch condor_q output at two-second intervals:

~~~
$ condor_submit tutorial01
Submitting job(s). 
1 job(s) submitted to cluster 823
$ watch -n2 condor_q username 
~~~

When your job has completed, it will disappear from the list.  To close watch, hold down Ctrl 
and press C.

##Job history##
Once your job has finished, you can get information about its execution from the condor_history command:

~~~
$ condor_history 823
 ID      OWNER            SUBMITTED     RUN_TIME ST   COMPLETED CMD
 823.0   username            8/21 09:46   0+00:00:12 C   8/21 09:46 /home/username/
~~~

You can see much more information about your job's final status using the -long option.


##Job output##
Once your job has finished, you can look at the files that HTCondor has returned to the 
working directory. If everything was successful, it should have returned: a log file from 
Condor for the job cluster: jog.log
* an output file for each job's output: job.output
* an error file for each job's errors: job.error
* a log  file for each job's log: job.log
Read the output file. It should be something like this:

~~~
$ cat job.output
Start time: Wed Aug 21 09:46:38 CDT 2013
Job is running on node: appcloud01
Job running as user: uid=58704(osg) gid=58704(osg) groups=58704(osg)
Job is running in directory: /var/lib/condor/execute/dir_2120
Sleeping for 10 seconds...
Et voila!
~~~ 

<div class="keypoints" markdown="1">

#### Key Points
*   HTCondor shedules and monitors your Jobs. 
*   To submit a job to HTCondor, the user need to prepare the Job execution and Job Submission scripts. 
*   *condor_submit* - HTCondor's job submission command.     
*   *condor_q* - HTCondor's job monitoring command.     
*   *condor_rm* - HTCondor's job removal command.     
</div>
