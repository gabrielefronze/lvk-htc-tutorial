---
title: IGWN DHTC Computing Tutorial - March 2021
tags: HTC, Virgo, LIGO, KAGRA, GEO600
description: Submitting jobs to LDG and OSG resources using HTCondor
slideOptions:
  theme: night
  transition: slide
---

<style type='text/css'>
.reveal pre code {
    max-height: 100vh;
}

.alert {
    font-size: 30px;
}
</style>

# LVK
# Computing Tutorial

###### *Gabriele Gaetano Fronzé - INFN Torino*
###### *James Alexander Clark - Georgia Tech and Caltech*

---

## This will be a <br>collaborative journey!

Bring on your laptop and get ready to join...

# 💻

----

### There are some requisites
1. Basic use of `git`
2. A bit of `ssh` and `bash`
4. Unix paths and environment variables
5. Good will 😁

---

# What's HTC?

----

HTC is a computing workflow manager focused on
> High
> Throughput
> Computing

Also known as

## HTCondor or Condor

# 🦅

----

HTCondor is the computing workflow manager adopted by the whole

# IGWN
##### International Gravitational-Wave Observatory Network


----

HTC provides a language to define the key concepts of a computing task:<br><br>

### 📥 Input data<br><br>

### ⚙️ Executable(s)<br><br>

### 📤 Output data


----

# 📄

## Such language is used in "submit files"
Submit files are sent to the HTC infrastructure to be evaluated and run

----

### This tutorial will introduce you to

### Condor's language 

### through some basic exercises

----

# 💡

:::warning
Such examples can be adapted for more generic tasks
:::

----

##### Head to GitHub or IGWN-GitLab to grab your own copy of the examples repository:

[Public GitHub](https://github.com/gabrielefronze/virgo-htc-tutorial.git)
[IGWN GitLab](https://git.ligo.org/gabriele.fronze/virgo-htc-tutorial.git)

:::warning
Tip: use `git clone` or even better 
`fork` the repository in your own workspace!
:::

----

# 📖

Additional information is available in the 

[official IGWN computing guide](https://computing.docs.ligo.org/guide/)

---

# Connect with HTC
### at
# IGWN

----

HTC is composed by several services.

# ⚙️

----

# 🗳

The `submit files` travel through the infrastructure to be matched with a valid computing resource.

----

# 💞=💻 

When the matching is done, the computing starts automatically retrieving inputs and sending outputs back to the submitter once done.

----

# 🌐

### The submit files start their travel through Condor from a submit node.

<br>

At the moment one submit node is ready at **Caltech** and another is about to be deployed at **Nikhef**.

<br>

##### For an always-up-to-date list head to [the guide](https://computing.docs.ligo.org/guide/condor/#access-points).

----

### Connection to the submit nodes can be achieved using:

* Interactively with no prior steps
    ```
    ssh <ligo username>@ssh.ligo.org
    ```

* After [x509 certificates registration](https://grouper.ligo.org/ldg/manage/)
    ```
    ligo-proxy-init <ligo username>
    gsissh <host name>
    ```
   
* After [ssh-key registration](https://grouper.ligo.org/ldg/manage_ssh/)
    ```
    ssh <ligo username>@<host name>
    ```

----

# 🎯
### Connect with
### `ldas-osg.ligo.caltech.edu`

----

### Two HTC Command Line tools we'll be using during the tutorial:

----

# 🎬

`condor_submit <submit file path>` 

to submit a computation task

```shell!
    $: condor_submit test-job.sub
    Submitting job(s).
    1 job(s) submitted to cluster 972902.
```

----

# ❓

`condor_q <job ID>` 

to assess a job status

```shell!
    $: condor_q 972902
    -- Schedd: schedd.ip.address : <192.168.1.1:9619?... @ 02/03/20 16:39:08
    OWNER    BATCH_NAME    SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS
    virgo029 ID: 972902   2/3  16:37      _      _      1      1 972902.0

    Total for query: 1 jobs; 0 completed, 0 removed, 1 idle, 0 running, 0 held, 0 suspended
    Total for all users: 739 jobs; 3 completed, 2 removed, 199 idle, 487 running, 48 held, 0 suspended
```


---

# Introduction to submit files language

---

## Your first submit file (maybe)

----

### Remember:

<br>

### 📥 Input data<br>

### ⚙️ Executable(s)<br>

### 📤 Output data

----

```scala=
universe = vanilla

transfer_input_files =
executable =
arguments =
transfer_output_files =

log = std.log
output = std.out
error = std.err

should_transfer_files = YES/NO
when_to_transfer_output = ON_EXIT
transfer_executable = True/False

accounting_group = something.somethingelse.somethingmore
accounting_group_user = albert.einstein

queue 1
```

----

```scala=
universe = vanilla

transfer_input_files = 📥
executable = ⚙️
arguments = ⚙️
transfer_output_files = 📤

log = std.log
output = std.out
error = std.err

should_transfer_files = YES/NO
when_to_transfer_output = ON_EXIT
transfer_executable = True/False

accounting_group = something.somethingelse.somethingmore
accounting_group_user = albert.einstein

queue 1
```

----

The line:

```scala
universe = vanilla
```

Is an HTC artifact you shouldn't worry about.

----

The lines:

```scala
log = std.log
output = std.out
error = std.err
```

Define where to redirect the job's standard output and error and the HTC log of the job. 

::: info
The logfile is always streamed to the submit node.
:::

::: warning
Standard output and error are propagated to the submit node only if:
```scala
when_to_transfer_output = ON_EXIT/ON_EXIT_OR_EVICT
```
is set as an option.
:::

----

The line:

```scala
should_transfer_files = YES/NO
```

Allows to explicitly enable/disable transfer of input and output files, in case these are already available on the working node.

----

The line:

```scala
transfer_executable = True/False
```

Allows to avoid transfer of the executable, in case these are already available on the working node.

----

### Accounting information is important (and mandatory)!


```scala
accounting_group = something.somethingelse.somethingmore
accounting_group_user = albert.einstein
```

##### Accounting information can be obtained using the 

##### [official accounting tag calculator](https://accounting.ligo.org/user)

<br>

##### The exercises folder is equipped with a pair of scripts able to bulk-set the accounting informations for all the exercises: 

<br>

##### `set_IGWN_user.sh` &emsp; &emsp; &emsp;`set_IGWN_group.sh`

----

## Let's move to the first exercise!

----

# 🎯
### Connect with
### `ldas-osg.ligo.caltech.edu`
### and clone the `git` repository

---

# Exercise 1

Input/output -less job

----

```scala=
universe = vanilla

executable = /bin/ls
arguments = -lrt .

log = std.log
output = std.out
error = std.err

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_executable = False

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

::: info
Note line 12 to avoid useless (and error prone) transfer of `ls`
:::

----

# 🎯
### Submit this exercise from
### `ldas-osg.ligo.caltech.edu`
### and checkout the outputs and logs during computing

---

# Exercise 2

Input-only job

----

```scala=
universe = vanilla

transfer_input_files = ./something_to_print.txt
executable = /bin/cat
arguments = ./something_to_print.txt

log = std.log
output = std.out
error = std.err

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_executable = False

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

----

# 🎯
### Submit the job and checkout the standard output

---

# Exercise 3

Output-only job

----

```scala
universe = vanilla

executable = /bin/touch
arguments = my_test_output_file.txt
transfer_output_files = ./my_test_output_file.txt

log = std.log
output = std.out
error = std.err

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_executable = False

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

----

# 🎯
### Submit the job find the back-propagated output `touch`-ed by the job

---

# Exercise 4

Input/output job

----

```scala
universe = vanilla

transfer_input_files = ./my_input.txt
executable = /bin/cp
arguments = my_input.txt my_output.txt
transfer_output_files = my_output.txt

log = std.log
output = std.out
error = std.err

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_executable = False

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

----

# 🎯
### Submit the job and compare input and output files

---

# Exercise 5

Script executing job

----

```scala
universe = vanilla

executable = ./script.sh
arguments = test_argument
transfer_output_files = ./surprise.txt

log = std.log
output = std.out
error = std.err

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_executable = True

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

----

# 🎯
### Have fun editing the script! 🎉🎉🎉

---

# Exercise 6
## *Bonus issue*

CVMFS executable job

----

```scala
universe = vanilla

transfer_input_files = 📥 //declare the necessary inputs
executable = /cvmfs/virgo.ego-gw.it/⚙️ //path to executable
arguments = ⚙️ //executable args
transfer_output_files = 📤 //declare the outputs here

log = std.log
output = std.out
error = std.err

should_transfer_files = YES/NO //make your choice
when_to_transfer_output = ON_EXIT
transfer_executable = False //since already distributed

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

:::warning
Note that the `/cvmfs/virgo.ego-gw.it` is not yet available in every IGWN computing center.
:::

---

# Exercise 7
## *Bonus issue*

CVMFS data job

----

```scala
universe = vanilla

transfer_input_files = /cvmfs/oasis.opensciencegrid.org/ligo/frames/📥 //declare the necessary inputs
executable = /cvmfs/virgo.ego-gw.it/⚙️ //path to executable
arguments = ⚙️ //executable args
transfer_output_files = 📤 //declare the outputs here

log = std.log
output = std.out
error = std.err

should_transfer_files = NO //since already distributed
when_to_transfer_output = ON_EXIT
transfer_executable = True/False //Make your choice

use_x509userproxy = true
requirements = (HAS_LIGO_FRAMES=?=True)

accounting_group = accounting_group_goes_here
accounting_group_user = albert.einstein

queue 1
```

:::warning
Note that the `/cvmfs/virgo.ego-gw.it` is not yet available in every IGWN computing center.
:::

----

The lines:

```scala
use_x509userproxy = true
requirements = (HAS_LIGO_FRAMES=?=True)
```

are needed to access `cvmfs` data.

##### The first line transfers the x509 proxy available on the submit node to the working node for granting authenticated access to data.

##### The second makes sure the selected computing node has the LIGO `cvmfs` namespace mounted.

---

# Magic...
# 🔮

----

The line:

```scala
queue 1
```

is a constant of our simple exercises.

##### Literally means "enqueue one job of this kind".

----

# ❓

## What happens with 
## `queue 100`

# ❓

----

100 copies of the same job are spawned.

<br>

::: warning
Not a big deal: nobody wants to run the same successful job more than once...
:::

<br>

#### What if the same `.sub` can be specialized using the job-ID?

----

All the jobs launched with the same `condor_submit` call are part of a `cluster`. 

The `cluster` ID can be accessed in submit files via `$(ClusterId)`.

<br>

Each job within a `cluster` has a job ID accessible via `$(ProcId)`.

----

This peculiarity allows to execute multiple jobs 

<br> 

### with a single submission command

<br>

by simply adding an algorithm to implement a 1:1 relationship between job ID and input/output sets.

----

Suppose one want to run 3 jobs, one for each of three files.

If the input files are named `file$(procId)` the submit file can be as easy as:

```scala=
executable = analyze.exe
arguments = file$(ProcId).in file$(ProcId).out
transfer_input_files = file$(ProcId).in

log = job_$(ClusterId)_$(ProcId).log
output = job_$(ClusterId)_$(ProcId).out
error = job_$(ClusterId)_$(ProcId).err

queue 3
```

----

Suppose one want to run 3 jobs, one for each of three files.

If the input files have different names than `file$(procId)`, the submit file is:

```scala=
executable = analyze.exe
arguments = $(infile).in $(infile).out
transfer_input_files = $(infile).in

log = job_$(ClusterId)_$(ProcId).log
output = job_$(ClusterId)_$(ProcId).out
error = job_$(ClusterId)_$(ProcId).err

infile = one
queue 1
infile = two
queue 1
infile = three
queue 1
```

----

or even better:

```scala
queue infile in (one two three)
```

----

Or the list of files can be specified using a file:

```scala
    queue infile from input_files_list.txt
```

where `input_files_list.txt` contains:

```bash
one
two
three
```

----

And the same pattern can be used for multiple parameters:

```scala=
executable = analyze.exe
arguments = $(infile).in $(outfile).out
transfer_input_files = $(infile).in

log = job_$(ClusterId)_$(ProcId).log
output = job_$(ClusterId)_$(ProcId).out
error = job_$(ClusterId)_$(ProcId).err

queue infile,outfile from params_list.txt
```

----

where `params_list.txt` contains:

```bash
one first
two second
three third
```

ingesting `one.in`, `two.in`, `three.in` 

and producing `first.out`, `second.out` and `third.out`

----

For further details and a deeper insight on this magic stuff head to:

[Advanced Submission Tutorial by John KNOELLER](https://agenda.hep.wisc.edu/event/1325/session/3/contribution/40)

---

# Wrap up

----

##### After this brief and incomplete tutorial you should be able to:

1. Submit basic Condor jobs to the IGWN computing grid
2. Understand how to incapsulate your computing tasks to run on Condor
3. (or at least understand what to change)
4. Be less scared of dealing with Condor and its [official guide](https://htcondor.readthedocs.io/en/stable/)

<br>
<br>

### We are at your disposal for additional support!

---

## Thanks for your attention

# ⁉️

### Questions? Comments?