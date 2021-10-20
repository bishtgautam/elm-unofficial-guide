Getting started
===============

Let's get a ELM simulation going and we will get into details about
certain setting (e.g. ``-res`` and ``compset``) in a later section.

1. Get the code 

.. code-block:: bash

   git clone https://github.com/E3SM-Project/E3SM
   cd e3sm
   git submodule update --init --recursive

2. Create a new case for a specified resolution ``-res <>`` and compset ``-compset <>`` on
   an E3SM-supported machine such as Cori, Compy, Anvil, Chrysalis, etc.
   
.. code-block:: bash
   
   cd cime/script

   # -case <value> : Path to your new case (e.g. `--case <path-to-the-case>/<case-name>`)
   # -res f09_f09  : 1deg global grid
   # -compset IELM: Present day; land and river models are active; atm is data-model
   
   ./create_newcase -case $PWD/IELM.f09_f09 -res f09_f09 -compset IELM

One can additionally provide information to ``create_newcase`` regarding the machine
on which the case is being created via ``--machine`` argument. On E3SM supported machine,
``create_newcase`` automatically detects the machine on which the case is being created.
However, certain machines support two machine configurations (e.g. Cori supports cori-hawell
and cori-knl configurations) and a user can choose one of the machine configurations
by ``--machine cori-knl`` or ``--machine cori-haswell``.
   
   
3. Make any modifications to the case

.. code-block:: bash

   cd IELM.f09_f09

   # Change the default 5-day run to be 1-year run
   ./xmlchange STOP_N=1,STOP_OPTION=nyears

   # Query the dir where the output will be saved
   ./xmlquery RUNDIR
   
   ./case.setup

4. Now build/compile the code

.. code-block:: bash

   ./case.build

5. Submit the case and get email notifications

.. code-block:: bash

   ./case.submit --mail-user john.doe@john-doe.org --mail-type all

6. After the model run is completed, the model output files will be in
   the run directory (``./xmlquery RUNDIR``).

.. code-block:: bash
  
   ls -l <RUNDIR>/*.elm.h0*.YYYY-MM.nc

7. Use your favorite scripting environment and make few type of plots such as

   - Monthly spatial plots
   - Monthly time series

   Few variable to consider for plots are

   - ``EFLX_LH_TOT`` Total latent heat flux [+ to atm] (2D data)
   - ``FSH``         Sensible heat (2D data)
   - ``H2OSOI``      Volumetric soil water (vegetated landunits only (3D data)

