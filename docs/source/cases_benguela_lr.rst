.. _section_benguela_lr:

Run BENGUELA_LR test case
=====================================



Installation
************

To install CROCO with ABL1d developments, get the dev_2022_ABL1d branch from INRIA's gitlab server :

.. code-block:: console

   git clone https://gitlab.inria.fr/croco-ocean/croco.git
   git checkout -b dev_2022_ABL1d origin/dev_2022_ABL1d




Prepare 3d atmospheric forcing fields for ABL1d
************************************************

Get the preprocessing tools for ABL1d
-------------------------------------------------

Get the preprocessing tools for ABL1d model with :

.. code-block:: console

   git clone https://github.com/JorisPianezze/preprocessing_for_abl1d.git

This repository contains the tool to pre-process atmospheric fields for the ABL1d model. This tool can read atmospheric fields from ECMWF products (IFS oper, ERA5, ERAI, ...). It allows to compute geostrophic pressure gradients, to interpolate the fields from the IFS vertical grid to the ABL1D vertical grid (which is defined in namelist) and to fill in the field values over land by extrapolating the values over sea.

This repository contains the following directories :

* src: contains the Makefile and the source codes in Fortran 90 ;

* ktest: contains the files necessary to launch the tool and prepare the ABL1d forcing for CROCO BENGUELA_LR case study.

Compilation
-------------------------------------------------

Go to src/ directory, modify the :file:`Makefile` according to your configuration and compile the code :

.. code-block:: console

   make

To clean the directory, you can use :command:`make clean`.

At the end of the compilation, 4 executables have been created :

* :file:`get_atm_LSfrc.exe` : calculate pressure gradients ;

* :file:`vinterp_atm_frc.exe` : vertically interpolate the IFS fields to the ABL1D; it is in this program that the vertical grid of the ABL1d is defined ;

* :file:`drown_atm_frc.exe` : fill in the values on land by extrapolating the values on sea (avoid strong gradients near the coast) ;

* :file:`slp_smoothing.exe` : test different methods of smoothing the surface pressure (Gibbs effect). Using this program is not necessary if you only want to create the forcing for the ABL1D.

All theses programs read a namelist called :file:`namelist_abl_tools`.

Download ERA5 data
------------------

Go to ktest/benguela_ifs_era5/0_input_data directory and launch the python script using CDSAPI :

.. code-block:: console

   python extract_era5_for_abl1d.py
   
At the end of the extraction you need to convert grib files into netcdf using the script :

.. code-block:: console

   ./convert_grib_to_netcdf.sh

Final netcdf file use for the preprocessing tool is called : :file:`era5_for_preprocessing_tools.nc`.

Run preprocessing tool
----------------------

Go to ktest/benguela_ifs_era5/1_run_preprocessing_tool_N_abl_50 to run preprocessing tools : 

.. code-block:: console

   ./run_preprocessing_tools.sh


Convert netcdf file into a format readable by CROCO 
---------------------------------------------------

Go to ktest/benguela_ifs_era5/2_convert_to_croco to run conversion scripts :

* :file:`create_croco_abl_from_ifs_era5.py` is used to create 3d atmospheric forcing field for ABL1d/CROCO (OFFLINE interpolation)

* :file:`create_croco_blk_from_ifs_era5.py` is used to create 2d atmospheric forcing field for classical bulk parametrization.


Compilation and simulation for BULK_FLUX test case
********************************

Modification in :file:`cppdefs.h` :

.. code-block:: console
   # define MPI
   # define BULK_FLUX
   # undef CLIMATOLOGY

.. code-block:: console
   ./jobcomp

.. code-block:: console

   mpirun -np 4 ./croco

Compilation and simulation for ABL1d test case
********************************

Modification in :file:`cppdefs.h` :

.. code-block:: console
   # define MPI
   # define ABL1D
   # undef CLIMATOLOGY

.. code-block:: console
   ./jobcomp

.. code-block:: console

   mpirun -np 4 ./croco


Results
********************************


