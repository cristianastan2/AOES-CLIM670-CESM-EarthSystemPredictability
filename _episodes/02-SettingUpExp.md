---
title: "Initialized Prediction"
questions:
- "How to setup an Initialized Prediction Experiment"
objectives:
keypoints:
- ""
---

### Create your case

For this experiment, we will use a different version of CESM and a different resolution.  The version of CESM we will use is cesm2.1.  It is located in `/glade.u/home/ssfcst/cesm2_1/`.  We will go to the `$CIMEROOT` in that version of the model to run `create_newcase`.

~~~
$ cd /glade/u/home/ssfcst/cesm2_1/cime/scripts
$ ./create_newcase --case ~/cases/cesm2fcst --compset BSSP585 --res f09_g17 --project UGMU0032
~~~
{: .language-bash}

### Go to our new case directory and run `case.setup`

~~~
$ cd ~/cases/cesm2fcst
$ ./case.setup
~~~
{: .language-bash}

### Configure our run

This configuration step is needed (need to explain why)

~~~
$ ./xmlchange --force CLM_NAMELIST_OPTS=use_init_interp=.true.
~~~
{: .language-bash}

### Configure our initial conditions

Our forecast will start on Jan 7, 2019 and run for 45 days. We first need to setup and tell it where to find our initial conditions.  We have 21 ensemble members where the Atmosphere Initial conditions have been slightly perturbed.  Each of you will run a different ensemble member, so each of you needs to get the correct initial conditions for your ensemble member.


Most of the initial conditions for various components of the model are located here in `/glade/scratch/ssfcst/FORKATHY/OTHERICS`

Create a directory in your `/glade/scratch/` for the initial conditions to be located and copy these initial conditions to the new directory.

~~~
$ mkdir -p /glade/scratch/kpegion/fcst/rest/2019-01-07
$ cp /glade/scratch/ssfcst/FORKATHY/OTHERICS/* /glade/scratch/kpegion/fcst/rest/2019-01-07/
~~~
{: .language-bash}

The atmosphere initial conditions are located in `/glade/scratch/ssfcst/FORKATHY/ATMIC/run.XX`, where `XX` refers to the ensemble member. Copy the file `/glade/scratch/ssfcst/FORKATHY/ATMIC/run.XX/b.e21.f09_g17.cam.i.2019-01-07-00000.nc' to your initial conditions directory.

~~~
$ cp /glade/scratch/ssfcst/FORKATHY/ATMIC/run.00/b.e21.f09_g17.cam.i.2019-01-07-00000.nc /glade/scratch/kpegion/fcst/rest/2019-01-07/
~~~
{: .language-bash}


Now that we have put the initial conditions in a particular location, we need to tell the case where to find them
~~~
$ ./xmlchange RUN_REFDIR=/glade/scratch/kpegion/fcst/rest/2019-01-07
$ ./xmlchange RUN_REFCASE=b.e21.f09_g17
~~~
{: .language-bash}

### Configure our startdate, refdate and run length

Now we need to give it the correct `RUN_STARTDATE`, `RUN_REFDATE`, `STOP_OPTION`, and `STOP_N`

~~~
$ ./xmlchange RUN_STARTDATE=2019-01-07
$ ./xmlchange RUN_REFDATE=2019-01-07
$ ./xmlchange STOP_OPTION=ndays
$ ./xmlchange STOP_N=45
~~~
{: .language-bash}

### Build and run your case.

~~~
qcmd -- ./case.build
./case.submit
~~~
{: .language-bash}

