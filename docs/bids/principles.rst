Basic BIDS Principles
==============================

This document will give a short and sweet introduction to the basic naming
system for BIDS-compliant niftis and their JSON sidecars as well as example
folder structures. For a much more comprehensive introduction to the BIDS
structure, see `the BIDS docs
<https://bids-specification.readthedocs.io/en/stable/01-introduction.html>`_


File Name Structure
------------------------------
File names are made from combinations of key-value pairs strung together. Some
key-value pairs are optional based on the experimental design, but others, such
as subject name and task type for fMRI scans, are not. Key-value pairs are
linked with hyphens while underscores separate each pair. For example, if a
participant with ID TS001 had a resting state scan, the portion of the scan name
telling us such would read ``sub-TS001_task-rest``. 

Since both hyphens and underscores are protected characters in the BIDS naming
system, they cannot be used in participant or scan IDs and should be removed
during BIDS conversion. Conversion tools can take care of that automatically,
but if you choose to manually convert to BIDS, you will need to include this
step in your pipeline.

A summary of all possible key-value pairs that make up a BIDS name as well as
whether they are optional or required can be found in the `entity table
<https://bids-specification.readthedocs.io/en/stable/99-appendices/04-entity-table.html>`_.


JSON sidecars
-------------------------------
Each nifti file in your study should also have an accompanying JSON file that
contains relevant metadata from the DICOM files that can be lost when converting
to nifti as well as pertinent information about the participant and study. The
accompanying JSON files can be complex and tedious to create manually. It is
highly recommended to use a semi-automatic DICOM to BIDS converter to generate
both the nifti and JSON files. Recommended converters are `dcm2bids
<https://github.com/UNFmontreal/Dcm2Bids>`_ and `heudiconv
<https://github.com/nipy/heudiconv>`_. A general guide to using heudiconv can be
found in this documentation as well.


Example Name Formats
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

