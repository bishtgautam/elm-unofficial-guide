Running ELM on your machine
===========================

Configuring your Mac for ELM

1. Xcode

-  Download and install Xcode

-  Install Xcode command line developers tool by running the following command on terminal `xcode-select --install` and click on Install button

-  Launch Xcode and accept licence.

2. Install denpendencies (Homebrew example)

.. code-block:: bash 

   brew install gcc cmake mpich netcdf

- Note: If you see an error while running create_newcase that indicates perl can't find XML::LibXML, you may need to install p5-xml-libxml as well

	-  for install XML::LibXML 

	.. code-block:: bash 

	   brew install libxml2
	   cpan XML::LibXML

3. Download E3SM code from github

.. code-block:: bash 

   git clone https://github.com/E3SM-Project/E3SM.git e3sm_trial 
   cd e3sm_trial
   git fetch origin
   git checkout origin/master
   git submodule update  --init  --recursive

-  Note: Need to setup SSH key for Github: https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

4. Setup machine
   
- Create a ``.cime`` folder in home directory

.. code-block:: bash
   mkdir ~/.cime

- Modify and copy ``config_compilers.xml`` and ``config_machines.xml`` from ``your-E3SM-dir/cime/config/e3sm/machines/userdefined_laptop_template/`` to ``~/.cime/``
   - Find examples of modification at https://gist.github.com/donghuix/434a73a2adfbf8816cb13d0e326bb93e

5. Run test case

.. code-block:: bash

   # Create a new case
   export RES=1x1_brazil
   export COMPSET=IELM
   export COMPILER=gnu
   export MACH=mac
   export CASE_NAME=${RES}.${COMPSET}.${COMPILER}

   cd your-path-to-e3sm/cime/scripts

   ./create_newcase -case ${CASE_NAME} -compset ${COMPSET} -res ${RES} -compiler ${COMPILER} -mach ${MACH}

   cd $CASE_NAME

   # Modify env_mach_pes.xml

   # Modify env_build.xml
   ./xmlchange -file env_build.xml -id MPILIB -val mpich
   ./xmlchange -file env_build.xml -id OS -val Darwin
   ./xmlchange -file env_build.xml -id CESMSCRATCHROOT -val ${PWD}
   ./xmlchange -file env_build.xml -id EXEROOT -val ${PWD}/bld

   # Modify env_run.xml
   ./xmlchange -file env_run.xml -id DATM_CLMNCEP_YR_END -val 2000
   ./xmlchange -file env_run.xml -id DATM_CLMNCEP_YR_START -val 2000
   ./xmlchange -file env_run.xml -id DATM_CLMNCEP_YR_ALIGN -val 1
   ./xmlchange -file env_run.xml -id RUNDIR -val ${PWD}/run

   # Setup
   ./case.setup

   # Download the input data
   ./check_input_data --download

   # Build the case
   ./case.build

   mkdir -p ${RUNDIR}/timing/checkpoints/

   # ./preview_run to see the command to run the case

Configuring your Windows for ELM: Coming soon.