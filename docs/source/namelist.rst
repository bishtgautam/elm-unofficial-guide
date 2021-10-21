Customizing ELM namelist
========================

Few useful changes to ``user_nl_elm``

.. _monthly_file:

Changing monthly output file
----------------------------

ELM by default outputs monthly history file in ``*elm.h0.**.nc`` files
that include many variables (>200). At times, many of the default output
variables may not be of interest, thus one could remove all default variables
(via ``hist_empty_htapes``) and only include select variables (via ``hist_fincl1``)
to the monthly history files by

.. code-block:: bash

   &elm_inparm
     hist_empty_htapes = .true.
     hist_fincl1 = 'TG', 'TV', 'FSA'
   /

.. _additional_files:

Saving additional output files
-------------------------------

ELM can output additional history files (such as ``*elm.h1.*.nc``, ``*elm.h2.*.nc``)
that have different temporal averaging (e.g. daily, hourly, every model timestep) via
``hist_nhtfrq`` where

- ``-24`` corresponds to daily average
- ``-1`` corresponds to hourly average
- ``0`` corresponds to monthly average
- ``1`` corresponds to each model time step

The number of time slices in these additional files can be controlled
vai ``hist_mfilt``.

.. code-block:: bash

   &elm_inparm
     hist_fincl2 = 'TG'
     hist_fincl3 = 'TV'
     hist_fincl4 = 'TG', 'TV', 'FSA'
     hist_nhtfrq = 0, -24, -1, 1
     hist_mfilt  = 12, 30, 24, 48
   /

Using the above-mentioned settings:

- Each ``*.elm.h1.*.nc`` will include 30 daily average values of ``TG``
- Each ``*.elm.h2.*.nc`` will include 24 hourly average values of ``TV``
- Each ``*.elm.h3.*.nc`` will include 48 values of ``TG``, ``TV``, and ``FSA`` at
  each model time step, which is typically is 30 min.
