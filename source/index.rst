.. UAB CINL documentation master file

========================================
Welcome to UAB CINL's documentation!
========================================

The Civitan International Neuroimaging Laboratory provides a core resource at
UAB for state-of-the-art MRI neuroimaging experiments and analyses for examining
brain and body anatomy and function both in health and disease. 

This houses technical information for CINL mainly pertaining to data storage,
organization, pre-processing, and processing on UAB's computing cluster Cheaha.
Cheaha provides researchers with both data storage space as well as a large
amount of computational power necessary for high-throughput neuroimaging
analysis. Access to the Cheaha cluster is free for UAB researchers. Request an
account using the following `instructions <https://docs.uabgrid.uab.edu/wiki/Cheaha_GettingStarted>`_

.. note::

   This project is under active development. To request changes or additions to
   the documentation, please submit an issue to the `main Github Repository <https://github.com/uabrc/CINL-Docs>`_


XNAT
====================

.. toctree::
    :maxdepth: 1
    :hidden:
    :caption: XNAT

    /xnat/account
    /xnat/projects
    /xnat/file-management

About XNAT
--------------------

XNAT is an open-source imaging informatics platform developed at Washington
University with a deployment here at UAB. It facilitates both MRI data storage
in a common structure as well as sharing across labs both internal and external
to UAB. It is free to use for all researchers at UAB. More information can be
found at `xnat.org <www.xnat.org>`_


Why Use XNAT
--------------------
- Can send data directly from the scanner to XNAT without copying to a flash drive
- Easy to add collaborators to projects and share data across labs
- Can act as a data backup
- Common storage system makes data preprocessing pipelines more generalizeable

This documenatation contains information on how to :doc:`create and manage
accounts <xnat/account.rst>`, :doc:`create projects <xnat/projects.rst>`, and
:doc:`upload and download data <xnat/projects.rst>`.


BIDS
==============================

.. toctree::
    :maxdepth: 1
    :hidden:
    :caption: BIDS

    /bids/principles.rst
    /bids/heudiconv.rst
    /bids/practical-heudiconv.rst
    /bids/heudiconv-scripts.rst


About BIDS
------------------------------

The BIDS data structure refers to a method of organizing and naming nifti and
associated JSON files for each MRI scan. Organizing data according to the BIDS
framework opens up access to various software applications that only act on BIDS
compliant datasets, such as fmriprep, mriqc, and qsiprep. Additionally, matching
structures across datasets makes navigation, exploration, and sharing of
datasets much easier. A short introduction to BIDS along with examples of BIDS
compliant file structures can be found in these docs. Read more about the BIDS
framework at `https://bids-specification.readthedocs.io/
<https://bids-specification.readthedocs.io/en/stable/>`__.


Conversion from DICOM to BIDS
------------------------------

While writing custom code to convert from DICOM files to the BIDS framework is
possible, tools have been to make this more automatic and reproducible across
datasets. This documentation provides information and examples on one of those
resources, :doc:`HeuDiConv <bids/heudiconv.rst>`.


fmriprep
==============================
.. toctree::
    :maxdepth: 2
    :hidden:
    :caption: fmriprep

    /fmriprep/intro.rst
    /fmriprep/fmriprep-on-cheaha.rst
    /fmriprep/examples.rst

About fmriprep
----------------------------- 

fmriprep is a BIDS app that performs minimal preprocessing on structural (T1w
and T2w) and BOLD data. fmriprep standardizes how functional data is
preprocessed across projects and labs through a user-friendly command line
interface. fmriprep can be used on the Cheaha cluster through Singularity
containers, but as of September 2021, the most recent version (v20.2.3) has been
installed as a module for ease-of-use. A short
:ref:`introduction<intro-fmriprep>` with an :ref:`example
script<example-ss-fmriprep>` using fmriprep on Cheaha is included in these docs.
Read more about fmriprep at https://fmriprep.org.
