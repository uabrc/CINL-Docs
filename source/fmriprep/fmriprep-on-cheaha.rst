Using fmriprep on Cheaha
========================

Full documentation on executing fmriprep can be found on its `ReadTheDocs
<https://fmriprep.org/en/stable/usage.html>`_. Instructions here will cover some
of the material from the main docs tailored to use on the Cheaha computing cluster at UAB.

The easiest way to access fmriprep on Cheaha is through using the module for
version 20.2.3 (the most recent as of September 2021) already installed on the
cluster. It is assumed at this point that all your data is stored in a valid
BIDS format. fmriprep can perform BIDS validation during runtime, or you can
perform BIDS validation `here
<https://bids-standard.github.io/bids-validator/>`__.

.. note::
    If you require an older version of fmriprep, it's suggested you
    download the version you need as a Singularity container. More information
    on use of fmriprep via Singularity can be found `here
    <https://fmriprep.org/en/20.2.2/singularity.html>`__. Singularity versions >=
    v2.5 are available as modules on Cheaha. 

Loading the Module
-------------------------
Cheaha users can access a variety of installed software through modules. Loading
the module to use in either an interactive session or in a batch job can be done
using the following command:

.. code-block::

    module load rc/fmriprep/20.2.3

From here, you have access to all commands available in the fmriprep toolbox,
mainly the ``fmriprep`` command. 

Required Positional Arguments
-----------------------------
The basic form of the ``fmriprep`` command is:

.. code-block::

    fmriprep [options] bids_dir output_dir analysis_level

- bids_dir: root folder of a valid BIDS dataset (sub-XXXXX folders are found the output path for the outcomes of preprocessing and visual reports)
- output_dir: the output path for the outcomes of preprocessing and visual reports. In a BIDS structure, this should be set to <bids_dir>/derivatives
- analysis_level: preprocessing stage to be run. 'participant' is the only available choice for fmriprep

These are the only options that must be listed every time fmriprep is run. A
multitude of optional argument, some of which are covered below, can define how
fmriprep behaves in with finer control. All options are listed on the `fmriprep
usage notes <https://fmriprep.org/en/stable/usage.html>`_. It is highly advised
to familiarize yourself with all available options for the fmriprep command
before use.

Select Optional Arguments
-------------------------
- \-\-work-dir: directory to place immediate outputs
- \-\-participant-label: list of participant identifiers ('sub-' tag optional)
- \-\-output-spaces: which standardized or native space to sample functional data onto. Options include standard MNI, native T1w, and many, many others.

.. note::
    Standardized atlas choices are those in TemplateFlow. Please view the
    `fmriprep documenation
    <https://fmriprep.org/en/stable/spaces.html?highlight=templateflow#templateflow>`_
    to use the ``output_spaces`` option correctly.

FreeSurfer Options
^^^^^^^^^^^^^^^^^^^
- \-\-fs-no-reconall: disable the recon-all portion of the pipeline
- \-\-fs-license-file: path to an available FreeSurfer license file. See :ref:`fs-license` for details

Performance Options
^^^^^^^^^^^^^^^^^^^
- \-\-n-cpus: total number of cpus available for the job
- \-\-mem: total amount of memory available for the job (in MB)
- \-\-omp-nthreads: maximum number of threads per process

Surface Preprocessing Options
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- \-\-cifti-output: output processed BOLD in CIFTI format. Choices are 91k or 170k



.. _fs-license:

FreeSurfer License File
-----------------------
Because FreeSurfer is a proprietary software package, it needs a license file to
run. A license file for FreeSurfer is provided on Cheaha. Run the following
command, replacing ``<destination>`` with the location you would like to copy
the license file to in your chosen workspace.

.. code-block::

    cp /share/apps/rc/software/FreeSurfer/7.1.1-centos7_x86_64/license.txt
    <destination>

Use the \-\-fs-license-file option followed by the path to your copied
license.txt. file to use FreeSurfer during data preprocessing.


Suggested Computational Resources
---------------------------------

While it is difficult to guess the exact correct amount of resources needed for
any given preprocessing job, general guidelines have been given `here
<https://fmriprep.org/en/stable/faq.html#how-much-cpu-time-and-ram-should-i-allocate-for-a-typical-fmriprep-run>`__.
Briefly, 4 CPUs and 5 GBs per CPU (20 total GBs) can complete preprocessing
(without FreeSurfer surface reconstruction) in approximately 2 hours. Including
surface reconstruction will dramatically increase the amount of time required,
but the resources required should be consistent. 

.. note:: 
    While increasing the amount of resources will decrease computation time,
    there is a limit, and no performance increase was seen past 16 CPUs.
    Recognize that requesting a large number of CPUs for each of multiple
    preprocessing jobs will increase the amount of time needed to allocate
    resources for that job to start, increasing total time from job submission
    to finish and functionally minimizing the gains of using a large amount of
    resources in the first place.

Even though the main documentation claims processing without surface
reconstruction can be performed in 2 hours, it is advised that extra time be
requested in case a processing step takes longer than normal. 6-12 hours on the
short partition when not performing recon-all, or 32-50 hours on the medium
partition (depending on the total number of scans to process) when performing
recon-all is suggested.

.. _example-ss-fmriprep:

Example Single-Subject Job Script
---------------------------------
This example script was written to process a single subject ``P01`` from
BIDS-formatted dataset ``D01``.

.. code-block:: bash
    
    #!/bin/bash
    #
    #SBATCH --job-name=P01-fmriprep
    #SBATCH --output=$USER_DATA/D01/jobs/out/P01-fmriprep-out.txt
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=4
    #SBATCH --partition=medium
    #SBATCH --time=50:00:00
    #SBATCH --mem-per-cpu=5000
    #SBATCH --mail-type=FAIL

    # load the module
    module load rc/fmriprep/20.2.3

    # run fmriprep
    fmriprep --work-dir $USER_DATA/D01/workdir/ \
             --participant-label P01 \
             --output-spaces T1w \
             --fs-license-file $HOME/license.txt \
             --n-cpus 4 \
             --omp-nthreads 4 \
             --cifti-output 91k \
             $USER_DATA/D01/nifti/ \
             $USER_DATA/D01/nifti/derivatives \
             participant

- The job requests 4 CPUs and 4 GBs of memory per CPU for 50 hours total on the medium partition. 
- The working directory was placed directly underneath the dataset directory
- The output space of the BOLD images was set to native T1w space as opposed to a normalized template space.
- The copied FreeSurfer license file was placed in my home directory and referenced in the script.
- All outputs were requested to be in cifti-space (91k default)
- The bids_dir, output_dir, and analysis_level were listed in that order after the options.

This script can be submitted to the scheduler using ``sbatch <script.sh>`` where
script.sh is the full path of the script (or just the script name if the
terminal working directory contains the script).


Example Array Job Script
-----------------------------------

SLURM job arrays are scripts made to easily replicate a job to be performed
across multiple inputs (i.e. multiple participants) while not taxing the job
scheduler. Read more about SLURM job arrays at their `documentation
<https://slurm.schedmd.com/job_array.html>`__.

This example script was written to process all subjects from the
``participants.tsv`` file in BIDS-formatted dataset ``D01``.

.. code-block:: bash

    #!/bin/bash
    #
    #SBATCH --job-name=fmriprep-%a
    #SBATCH --output=fmriprep-%A-%a.txt
    #SBATCH --ntasks=1
    #SBATCH --cpus-per-task=5
    #SBATCH --partition=medium
    #SBATCH --time=50:00:00
    #SBATCH --mem-per-cpu=4G
    #SBATCH --mail-type=FAIL

    # set the bids-formatted directory. 
    bidsdir=$USER_DATA/D01/nifti/

    # set the participant id from the participants.tsv file in the bidsdir
    pid=$(awk "NR==$(($SLURM_ARRAY_TASK_ID+1)){print;exit}" $bidsdir/participants.tsv | cut -f 1)

    # load the module
    module load rc/fmriprep/20.2.3

    # run fmriprep
    fmriprep --work-dir $USER_DATA/D01/workdir/ \
             --participant-label $pid \
             --output-spaces T1w \
             --fs-license-file $HOME/license.txt \
             --n-cpus 4 \
             --omp-nthreads 4 \
             --cifti-output 91k \
             $bidsdir \
             $bidsdir/derivatives \
             participant

This script will replicate the fmriprep command for participants in the
``participants.tsv`` file. When submitting this job, include the
``--array=<min>-<max>`` option in the ``sbatch`` command representing the index
of the participants you want to run. For example, if you want to run the first
10 participants in the file, use ``--array=1-10``, whereas if you want to run
the 7th and 10th participant only, use ``--array=7,10``. 

This script can be placed in and run from a ``code`` folder in the BIDS-sorted
``nifti`` folder to maintain BIDS compliance.