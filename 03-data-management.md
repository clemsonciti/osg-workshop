---
layout: lesson
title: Data storage and transfer
---

## Objectives {.objectives}

*   Discover how to transfer input and output data  

### `home`

Home is meant for storing files for quick access.
Usually, files such as program files, parameter files, scripts, etc.
are kept in your /home directory.
Although disk quota is not imposed on home,
we recommend a disk usage of less than 10 GB.
Home filesystem is not suitable to run your HTCondor jobs.
It is a good practice to run all your jobs under the stash directory.

### `stash`

Stash provides a large temporary storage.
Stash is the place you run all your HTCondor jobs.
Like home, there is no disk quota imposed on stash.
However, the data on stash is not backed-up,
so transfer the data to a secondary local disk (such as your local desktop, laptop, etc.,)
on a regular basis. For data transfer of more than 10 GB,
use the Globus transfer service.

### `public`

Anybody could read the files under ~/public.
If you want to share some files with your collaborators,
including those don't have any account on OSG,
keep the files under `~/public`.
The ~public is available via WWW as http://stash.osgconnect.net/+username.
The data on `~/public` is accessible to the jobs on remote worker machine via the wget command.

## Submitting a job with 


