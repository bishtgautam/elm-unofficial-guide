Creating custome ELM surface data
===========================

Currently, this workflow only works on NERSC cori. 

1. SCRIP mesh

-  Create `SCRIP <https://earthsystemmodeling.org/docs/release/ESMF_8_0_1/ESMF_refdoc/node3.html#SECTION03028000000000000000>`_ file for you new surface grid. 

-  If you already have the domain file for your grid, you can convert it to SCRIP with the `MATLAB script <https://github.com/donghuix/Setup-E3SM-Mac/blob/master/matlab-scripts-to-process-inputs/convert_domain_to_SCRIPgrid.m>`_.

2. Mapping file

- There are 16 raw datasets. For each raw dataset, create a mapping file to go from raw data to your new grid.

- reference: https://github.com/bishtgautam/elm-surface-dataset

.. code-block:: bash 

   git clone https://github.com/bishtgautam/elm-surface-dataset
   cd elm-surface-dataset
   ./create_mapping_scripts.sh         \
    -hgrid_name icom_maps              \
    -scrip_filename icom_mpas_scrip.nc \
    -scrip_filepath <path-to-icom-mpas-scrip.nc>

3. Compile mksurfdata_map

- Use components/elm/tools/mksurfdata_map to create the surface dataset

- Input to the mksurfdata_map tool is a namelist file (mksurfdata_map.namelist) that has information about the 16 mapping files (generated in step-2) and 16 raw datasets.

.. code-block:: bash 

   # Compiling mksurfdata_map on NERSC
    
   # On NERSC
   module purge
   module load intel
   module load cray-netcdf-hdf5parallel/4.6.3.2
   module load cray-parallel-netcdf/1.11.1.1
   module load cray-hdf5-parallel/1.10.5.2
   export LIB_NETCDF=$NETCDF_DIR/lib
   export INC_NETCDF=$NETCDF_DIR/include
   export USER_FC=ifort
   export USER_CC=icc
   export USER_LDFLAGS="-L$NETCDF_DIR/lib -lnetcdf -lnetcdff -lnetcdf_intel"
   export USER_LDFLAGS=$USER_LDFLAGS" -L$HDF5_DIR/lib -lhdf5 -lhdf5_fortran"
    
    
   cd components/elm/tools/mksurfdata_map/src
   make
    
   # If everything works 'mksurfdata_map' is created in components/elm/tools/mksurfdata_map/

- Create a namelist for mksurfdata_map, and replace the mapping file that generated in the following example.

.. code-block:: bash 
# On NERSC use /global/cfs/cdirs/e3sm/inputdata/lnd/clm2/rawdata for mksrf_*
 
>cat northamericax4v1pg2-2010.namelist
   &elmexp
    nglcec            = 0
    mksrf_fgrid       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fpft          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fglacier      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_3x3min_GLOBE-Gardner_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fsoicol       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fsoiord       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_furban        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_3x3min_LandScan2004_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fmax          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_3x3min_USGS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_forganic      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_5x5min_ISRIC-WISE_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_flai          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fharvest      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_flakwat       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_3x3min_MODIS_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fwetlnd       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fvocef        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fsoitex       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_5x5min_IGBP-GSDP_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_furbtopo      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_10x10min_nomask_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_flndtopo      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_10x10min_nomask_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fgdp          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fpeat         = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fabm          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_ftopostats    = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_1km-merge-10min_HYDRO1K-merge-nomask_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fvic          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.9x1.25_GRDC_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fch4          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_360x720cru_cruncep_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fphosphorus   = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_GSDTG2000_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fgrvl         = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_5x5min_ISRIC-WISE_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fslp10        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    map_fero          = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/mappingdata/maps/northamericax4v1pg2/map_0.5x0.5_AVHRR_to_northamericax4v1pg2_nomask_aave_da_c210112.nc'
    mksrf_fsoitex     = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_soitex.10level.c010119.nc'
    mksrf_forganic    = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_organic_10level_5x5min_ISRIC-WISE-NCSCD_nlev7_c120830.nc'
    mksrf_flakwat     = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_LakePnDepth_3x3min_simyr2004_c111116.nc'
    mksrf_fwetlnd     = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_lanwat.050425.nc'
    mksrf_fmax        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_fmax_3x3min_USGS_c120911.nc'
    mksrf_fglacier    = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_glacier_3x3min_simyr2000.c120926.nc'
    mksrf_fvocef      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_vocef_0.5x0.5_simyr2000.c110531.nc'
    mksrf_furbtopo    = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_topo.10min.c080912.nc'
    mksrf_flndtopo    = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/topodata_10min_USGS_071205.nc'
    mksrf_fgdp        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_gdp_0.5x0.5_AVHRR_simyr2000.c130228.nc'
    mksrf_fpeat       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_peatf_0.5x0.5_AVHRR_simyr2000.c130228.nc'
    mksrf_fabm        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_abm_0.5x0.5_AVHRR_simyr2000.c130201.nc'
    mksrf_ftopostats  = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_topostats_1km-merge-10min_HYDRO1K-merge-nomask_simyr2000.c130402.nc'
    mksrf_fvic        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_vic_0.9x1.25_GRDC_simyr2000.c130307.nc'
    mksrf_fch4        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_ch4inversion_360x720_cruncep_simyr2000.c130322.nc'
    outnc_double      = .true.
    all_urban         = .false.
    no_inlandwet      = .true.
    mksrf_furban      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_urban_0.05x0.05_simyr2000.c120621.nc'
    mksrf_fphosphorus = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_soilphos_0.5x0.5_simyr1850.c170623.nc'
    mksrf_fgrvl       = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_gravel_10level_5min.c190603.nc'
    mksrf_fslp10      = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_slope_10p_0.5x0.5.c190603.nc'
    mksrf_fero        = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/mksrf_soilero_0.5x0.5.c190603.nc'
    mksrf_fvegtyp  = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/LUT_LUH2_HIST_LUH1f_07082020/LUT_LUH2_historical_2010_c07082020.nc'
    mksrf_fsoicol  = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/pftlandusedyn.0.5x0.5.simyr1850-2005.c090630/mksrf_soilcol_global_c090324.nc'
    mksrf_fsoiord  = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/pftlandusedyn.0.5x0.5.simyr1850-2005.c090630/mksrf_soilord_global_c150313.nc'
    mksrf_flai     = '/global/cscratch1/sd/gbisht/inputdata/lnd/clm2/rawdata/pftlandusedyn.0.5x0.5.simyr1850-2005.c090630/mksrf_lai_global_c090506.nc'
    fsurdat        = 'surfdata_northamericax4v1pg2_simyr2010_c210112.nc'
    fsurlog        = 'surfdata_northamericax4v1pg2_simyr2010_c210112.log'
    mksrf_fdynuse  = ' '
    fdyndat        = ' '
   /
    
   # On NERSC use an interactive job to create the data
   salloc --nodes 1 --qos interactive --time 01:00:00 --constraint knl -A e3sm
    
   export HDF5_USE_FILE_LOCKING=FALSE
   srun -n 1 ./mksurfdata_map < northamericax4v1pg2-2010.namelist
