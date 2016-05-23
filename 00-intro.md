---
layout: lesson
title: Introduction to Open Science Grid 
---

## Objectives {.objectives}
*   Get to know what is Open Science Grid
*   What resources are open to academic researchers
*   Computation that is a good match for OSG Connect
*   Computation that is NOT a good match for OSG Connect

## Introduction to Open Science Grid (OSG)  

The Open Science Grid (OSG) is a consortium of research communities who promote science 
via sharing of computing resources. The Open Science Grid (OSG)

- enables distributed computing on more than 120 institutions,
- supports efficient data processing and
- provides large scale scientific computing of 2 million core CPU hours per day.

The resources accessible through the OSG are contributed by the community, organized by 
the OSG, and governed by the OSG Consortium. The cores that are free in the OSG shared pool 
are made available to the users. These are 
opportunistic resources for the users and the number of freely available 
cores are varying at any given time. 

## Computation that is a good match for OSG Connect 

High throughput workflows with simple system and data dependencies are a good 
fit for OSG Connect. Typically these workflows can be decomposed into multiple
tasks that can be carried out independently.  Ideally, these tasks will download 
data for input, run some computation on it and then return results (which may be 
used by future tasks).

Jobs submitted into the OSG Connect will be executed on machines at several 
remote physical clusters. These machines may differ in terms of computing 
environment from the submit node. Therefore it is important that the jobs are 
as self-contained as possible by generic binaries and data that can be either 
carried with the job, or staged on demand. Please consider the following 
guidelines:

-   Software should preferably be single threaded, using less than 2 GB memory and 
    each invocation should run for 1-12 hours (optimally under 4 hours). There is 
    some support for jobs with longer run time, more memory or multi-threaded codes. 
    Please contact the support listed below for more information about these 
    capabilities.
-   Only core utilities can be expected on the remote end. There are no standard 
    versions of software such as 'gcc', 'python', 'BLAS' or others on the grid. 
    Consider using Distributed Environment Modules to manage software dependencies, 
    or read our Developing High-Throughput Applications guide.
-   Input and output data for each job should be < 10 GB to allow them to be 
    transferred in by the jobs, processed and returned to the submit node. Note 
    that the OSG Connect Virtual Cluster does not have a global shared file 
    system, so jobs with such dependencies will not work.
-   No shared filesystem. Jobs must transfer all executables, input data, and 
    output data. HTCondor can transfer the files for you, but you will have to 
    identify and list the files in your HTCondor job description file. 

## Computation that is NOT a good match for OSG Connect 

The following are examples of computations that are NOT good matches for 
OSG Connect:
-   Tightly coupled computations, for example MPI based communication, will 
    not work well on OSG Connect due to the distributed nature of the infrastructure.
-   Computations requiring a shared file system will not work, as there is 
    no shared filesystem between the different clusters on OSG Connect.
-   Computations requiring complex software deployments or proprietary software 
    are not a good fit.  There is limited support for distributing software to 
    the compute clusters, but for complex software, or licensed software, 
    deployment can be a major task.

## How to get help using OSG Connect

Please contact user support staff at [connect-support@uchicago.edu](mailto:connect-support@uchicago.edu).

## Available Resources on OSG

Commonly used software and libraries on the Open Science Grid are available in a
central repository (known as OASIS) and accessed via the *module* command. We will see how to 
search for, load, and use software packages.

We will also cover the usage of the built-in *tutorial* command. Using *tutorial*,
we load a variety of job templates that cover basic usage, specific use cases, and best practices.

## Logging in to OSG

You can connect to the OSG through the Palmetto cluster.
This will allow you to submit OSG jobs that use data and programs
that are stored on the Palmetto cluster.

First, log in the Palmetto cluster as usual:

~~~
$ ssh palmetto_username@user.palmetto.clemson.edu
~~~

Then, switch from the Palmetto modules to the OSG connect modules:

~~~
$ source /software/connect-client-0.5.3/bin/switch-modules-oasis
~~~

Load the `connect-client` module:

~~~
$ module load connect-client
~~~

Now, connect to the OSG:

~~~
$ connect setup <globusID>
~~~

~~~
$ connect test
~~~

## Projects and accounting

CPU time is accounted by projects. You can check your current accounting
project:

~~~
$ connect project
~~~

You can also get a list of all projects that you are a part of:

~~~
$ connect show-projects
~~~

After today, you will probably want to join an
[existing project](osgconnect.net/project-summary)
or [create a project](http://osgconnect.net/newproject) associated with
your research.

## Tutorial Command

The built-in `tutorial` command assists a user in getting started on 
OSG.  To see the list of existing tutorials, type

~~~
$ tutorial # will print a list tutorials
~~~

Say for example, you are interested in learning how to run R scripts on OSG, the 
tutorial command sets up the R tutorial for you. 

~~~
$ tutorial R  # prints the following message:


Application Example - R (statistical analysis)

This tutorial will introduce you to using the R statistical programming
language on OSG Connect. By the end of the tutorial:

   * You will have set up R from the OSG OASIS service on the submit host
   * You will know how to use the HAS_CVMFS_oasis_opensciencegrid_org job steering requirement. 

Tutorial 'R' is set up.  To begin:
     cd ~/osg-R
~~~ 

The "tutorial R" command creates a directory "osg-R" containing the neccessary script and input files. 

~~~
mciP.R      # The example R script file
R-wrapper.sh # The job execution file 
R.submit  # The job submission file (will discuss later in the lesson HTCondor scripts)
~~~

Lets focus on "mciP.R" and the "R-wrapper" scripts. The details of "R.submit" script 
will be discussed later when we learn HTCondor scripts.  

The file "mciP.R" is a R script that calculates the value of *pi* using the Monte Carlo
method.  The R-wrapper.sh essentially loads the R module and runs the "mciP.R" 
script. 

~~~
#!/bin/bash # Defines the shell environment.
module load R    # Loads the module 
Rscript  mcpi.R  # Execution of the R script
~~~

Similar to the R tutorial, there are other tutorials available on OSG. The available 
tutorials serve as templates to develop your own scripts and run the 
calculations on OSG. 

## Key Points
*   OSG resources are distributed across 120 institutions and  supports scientific computing of 2 million core CPU hours per day.   
*   Many scientific applications are installed on OSG and available for the users. 
*   To use an existing application use the module load command. 
*   The command - *tutorial* helps to access the existing tutorials.  
