---
layout: lesson
title: HTCondor scripts  
---

## Objectives {.objectives}
*   Learn how to submit HTCondor Jobs.   
*   Learn how to monitor the running Jobs.    

## Overview
In this section, we will learn the basics of HTCondor in submitting and monitoring jobs. The jobs are 
submitted through the login node of OSG Connect. The submitted jobs are executed on the remote worker 
node(s) and the outputs are transfered back to the login node. In the HTCondor job submit file, we have 
to describe how to execute the program and transfer the output data. 

## Hello world on OSG

As our first job, we'll simply submit a series of jobs
that each print a job ID (the first will print 0, the second will print 1, and so on...).
So here's the expected output from each job:

~~~
Hello world! I am job 0
~~~

~~~
Hello world! I am job 1
~~~

~~~
Hello world! I am job 2
~~~

.. and so on.

First, let's write a simple bash script that
will perform this task:

~~~
#!/bin/bash

# hello.sh

echo "Hello world! I am job $1"
~~~

Let's test that the script works:

~~~{.input}
$ chmod u+rx hello.sh
~~~

~~~{.input}
$ ./hello.sh 100
~~~

~~~{.outpu}
Hello world! I am job 100
~~~

~~~{.input}
$ ./hello.sh 2
~~~

~~~
Hello world! I am job 2
~~~

Now, we'll write a HTCondor job script
to submit several jobs, each of which will run `hello.sh`.

~~~
# hello.submit

# The UNIVERSE defines an execution environment. You will almost always use VANILLA. 
universe = vanilla

# EXECUTABLE is the program your job will run.
Executable = hello.sh

# Any arguments that will be passed to the EXECUTABLE:
arguments = $(Process)

# ERROR and OUTPUT are the error and output channels from your job
# that HTCondor returns from the remote host.
error = err.$(Process)
output = out.$(Process)

# The LOG file is where HTCondor places information about your 
# job's status, success, and resource consumption. 
log = log.$(Process)

# QUEUE is the "start button" - it launches any jobs that have been 
# specified thus far. 
queue 5
~~~

To submit the jobs, run `condor_submit`:

~~~{.input}
$ condor_submit hello.sh
~~~

The condor_q command tells the status of currently running jobs. Generally you will want to limit it to your own jobs:

~~~
$ condor_submit hello.submit
Submitting job(s).....
5 job(s) submitted to cluster 21629556.
~~~

~~~
$ condor_q atrikut


-- Submitter: login01.osgconnect.net : <192.170.227.195:47086> : login01.osgconnect.net
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
 21629556.0   atrikut         5/22 19:15   0+00:00:00 I  0   0.0  hello.sh 0
 21629556.1   atrikut         5/22 19:15   0+00:00:00 I  0   0.0  hello.sh 1
 21629556.2   atrikut         5/22 19:15   0+00:00:00 I  0   0.0  hello.sh 2
 21629556.3   atrikut         5/22 19:15   0+00:00:00 I  0   0.0  hello.sh 3
 21629556.4   atrikut         5/22 19:15   0+00:00:00 I  0   0.0  hello.sh 4

 5 jobs; 0 completed, 0 removed, 5 idle, 0 running, 0 held, 0 suspended
~~~

Note the ST (state) column. Your job will be in the I state (idle) if it hasn't 
started yet. If it's currently scheduled and running, it will have state R (running).
If it has completed already, it will not appear in condor_q.

Let's wait for your job to finish – that is, for `condor_q` not to show the job in its output.
A useful tool for this is `watch` – it runs a program repeatedly,
letting you see how the output differs at fixed time intervals.
Let's submit the job again, and watch condor_q output at two-second intervals:

~~~
$ watch -n2 condor_q username 
~~~

When your job has completed, it will disappear from the list.  To close watch, hold down Ctrl 
and press C.

## Job history
Once your job has finished, you can get information about its execution from the condor_history command:

~~~
$ condor_history 21629556
 ID     OWNER          SUBMITTED   RUN_TIME     ST COMPLETED   CMD
 21629556.4   atrikut         5/22 19:15   0+00:00:04 C   5/22 19:21 /home/atrikut/python-example/hello.sh 4
 21629556.3   atrikut         5/22 19:15   0+00:00:39 C   5/22 19:21 /home/atrikut/python-example/hello.sh 3
 21629556.1   atrikut         5/22 19:15   0+00:03:45 C   5/22 19:20 /home/atrikut/python-example/hello.sh 1
 21629556.2   atrikut         5/22 19:15   0+00:00:48 C   5/22 19:20 /home/atrikut/python-example/hello.sh 2
 21629556.0   atrikut         5/22 19:15   0+00:02:55 C   5/22 19:19 /home/atrikut/python-example/hello.sh 0
~~~

You can see much more information about your job's final status using the `-long` option.

## Job output

Once your job has finished, you can look at the files that HTCondor has returned to the 
working directory. If everything was successful, it should have returned:

*   an output file for each job's output
*   an error file for each job's errors
*   a log  file for each job's log

## Key Points

*   HTCondor shedules and monitors your Jobs. 
*   To submit a job to HTCondor, the user need to prepare the Job execution and Job Submission scripts. 
*   *condor_submit* - HTCondor's job submission command.     
*   *condor_q* - HTCondor's job monitoring command.     
*   *condor_rm* - HTCondor's job removal command.     
