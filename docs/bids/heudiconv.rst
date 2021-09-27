HeuDiConv
================================

HeuDiConv stands for Heuristic Dicom Converter and is a flexible pipeline for
converting DICOMs to a BIDS formatted data structure. Using dcm2niix, conversion
to the nifti formmat is fast and requires minimal user input. This document aims
to instruct users on how to install and use HeuDiConv on the Cheaha computing
cluster. HeuDiConv's full documentation is outdated but can be found `here for
further information <https://heudiconv.readthedocs.io/en/latest/index.html>`__.


Installation
-------------------------------

The simplest method to install Heudiconv is through use of Singularity
containers. Containers are stand-alone instances that provide all the necessary
dependencies for a given program out-of-the-box so no management of external
programs is required. Using this method, you will download a Singularity image
file containing Heudiconv and all of its dependencies in a single location in
your personal user space or a shared lab project space. More information on
using Singularity containers can be found at their `documentation
<https://sylabs.io/guides/3.8/user-guide/>`__.

In order to use Singularity on Cheaha, you will need to load the module. You
only need to load the module once when opening a new terminal window or in a job
submission script. The command to load the latest version of Singularity
installed on the cluster is:

.. code-block:: bash

    module load Singularity

To load a specific version, use ``module spider Singularity`` to view all
installed Singularity modules and load the one you want.

The container for Heudiconv can be found on their `DockerHub page
<https://hub.docker.com/r/nipy/heudiconv>`__, and the source code can be found
on their `github page <https://github.com/nipy/heudiconv>`__. When downloading
the image file, monitor these pages for updated releases. As of September 2021,
the latest released version of Heudiconv is 0.9.0.

To begin, open a terminal window on Cheaha either through the HPC Desktop portal
at `<rc.uab.edu>`__ or through your own personal VNC session. If you are working
through a personal VNC session, be sure to start an interactive session in your
terminal first to avoid running anything on the login node. 

To pull and build the latest version of Heudiconv, run the following commands:

.. code-block:: bash
    
    singularity pull $USER_DATA/heudiconv-0.9.0.sif docker://nipy/heudiconv:0.9.0

This command will pull Heudiconv version 0.9.0 from DockerHub, convert it to a
singularity image, and save it in your user data folder as heudiconv-0.9.0.sif.
Modify the output path as you see fit to save it where you need it. The download
and conversion process will take some time, so be patient while everything runs.


Initial Folder Structure
------------------------------------

Once the Singularity image has been downloaded, make sure your DICOM files are
organized consistently across the project you are converting. For instance, a
preferred organization is:

.. code-block:: text

    |-- dataset/
        |-- dicom
        |   |-- subject-01/
        |   |   |-- [sess-01/]
        |   |   |   |-- scan-01/
        |   |   |   |   |-- ***001.dcm
        |   |   |   |   |-- ***002.dcm
        |   |   |   |   |-- ...
        |   |   |   |
        |   |   |   |-- scan-02/
        |   |   |       |-- ***.dcm
        |   |   |
        |   |   |-- [sess-02/]
        |   |
        |   |-- subject-02/
        |   |
        |   |-- subject-03/
        |
        |-- nifti (empty)

Inclusion of the session directory level is optional if there is only one
session per participant. The names of the dicom files themselves do not need to
be altered in any way before running Heudiconv.

If your data is stored in a different format but has a consistent structure
across all files, this is fine. It is just important that the subject name as
well as session number (if multiple sessions were acquired) are easily extracted
from the file path.

The HeuDiConv CLI
-----------------------------------



Running HeuDiConv
-----------------------------------

Step 1
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The first step in Heudiconv generates 