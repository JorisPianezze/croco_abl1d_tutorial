.. _section_kilpatrick:

Run KILPATRICK test cases
=====================================

.. _installation:


Cold-to-warm case
*****************

Installation
------------

To install CROCO, get the dev_2022_ABL1d branch from INRIA's gitlab server :

.. code-block:: console

   git clone https://gitlab.inria.fr/croco-ocean/croco.git
   git checkout -b dev_2022_ABL1d origin/dev_2022_ABL1d 

Compilation
-----------

To compile Kilpatrick test case, you need to adapt the cppdefs.h file (define KILPATRICK and undef REGIONAL) :

.. code-block:: console

   ...
   #define KILPATRICK      /* 2D sst front*/
   /*
        ... OR REALISTIC CONFIGURATIONS
   */
   #undef  COASTAL         /* COASTAL Applications */
   #undef  REGIONAL        /* REGIONAL Applications */
   ...

In param.h, you need to change the MPI setup by :

.. code-block:: fortran

   parameter (NP_XI=4,  NP_ETA=1,  NNODES=NP_XI*NP_ETA)

Simulation
----------

Here is an example of the croco.in file to use :

.. code-block:: console

   title:
        KILPATRICK
   time_stepping: NTIMES   dt[sec]  NDTFAST  NINFO
                1296      100      2      1

   S-coord: THETA_S,   THETA_B,    Hc (m)
           7.0d0     2.0d0      200.0d0

   initial: NRREC / filename
          0
      croco_ini.nc
   restart:          NRST, NRPFRST / filename
                   2592    -1
      croco_rst.nc

   history: LDEFHIS, NWRT, NRPFHIS / filename
            T      36     0
      croco_his.nc
   averages: NTSAVG, NAVG, NRPFAVG / filename
            1      36     0
      croco_avg.nc
   primary_history_fields: zeta UBAR VBAR  U  V   wrtT(1:NT)
                          T    T   T   T  T    40*T
   auxiliary_history_fields:   rho Omega  W  Akv  Akt  Aks  Bvf  Visc3d Diff3d  HBL HBBL Bostr Bustr Bvstr Wstr Ustr Vstr Shfl Swfl rsw rlw lat sen HEL
                             F   F     T   F    T    F   F    F       F       T   T    T     F      F     T    T    T    T    T   30*T

   primary_averages: zeta UBAR VBAR  U  V   wrtT(1:NT)
                   T    T    T    T  T   40*T
   auxiliary_averages: rho Omega  W  Akv  Akt  Aks  Bvf Visc3d Diff3d HBL HBBL Bostr Bustr Bvstr Wstr Ustr Vstr Shfl Swfl rsw rlw lat sen HEL
                     F   T     T   F    T    F    F    F     F      T   T    T     F     F     T   T    T     T    T   30*T

   abl: ldefablhis, nwrtablhis, nrpfablhis / filename
              T       36     0
      croco_abl_his.nc
   abl_history_fields: pu_dta pv_dta pt_dta pq_dta pgu_dta pgv_dta u_abl v_abl t_abl q_abl tke mxlm mxld avm avt ablh zr zw Hzr Hzw
                      T      T       T      T       T      T     T      T     T     T     T   T    T    T   T   T   T  T  T   T
   abl_averages: ldefablavg, ntsablavg, nwrtablavg, nrpfablavg / filename
                  F        1             1          0
      croco_abl_avg.nc
   abl_averages_fields: pu_dta pv_dta pt_dta pq_dta pgu_dta pgv_dta u_abl v_abl t_abl q_abl tke mxlm mxld avm avt ablh zr zw Hzr Hzw
                      T      T       T      T       T      T     T      T      T     T     T   T    T    T   T   T   T  T  T   T

   rho0:
      1025.d0

   vertical_mixing: Akv_bak, Akt_bak [m^2/sec]
                   0.d0    30*0.d0

   bottom_drag:     RDRG [m/s],  RDRG2,  Zob [m],  Cdb_min, Cdb_max
                 3.0d-04      0.d-3    0.d-3     1.d-4    1.d-1

   gamma2:
                 1.d0

   abl_nudg_cof:    ltra_min, ltra_max, ldyn_min, ldyn_max  [seconds for all]
                      5400.     3600.     5400.    3600.

   nudg_cof:    TauT_in, TauT_out, TauM_in, TauM_out  [days for all]
                  1.      360.      3.      360.



Launch the simulation with mpirun (it takes less than 10 secondes) :

.. code-block:: console
   
   mpirun -np 4 ./croco

Results
-------

At the end of the simulation you should have the file croco_abl_his.nc. It contains instantanneous ABL1d variables.

Warm-to-cold case
*****************

Installation
------------

To install CROCO, get the dev_2022_ABL1d branch from INRIA's gitlab server :

.. code-block:: console

   git clone https://gitlab.inria.fr/croco-ocean/croco.git
   git checkout -b dev_2022_ABL1d origin/dev_2022_ABL1d 

Compilation
-----------

To compile Kilpatrick test case, you need to adapt the cppdefs.h file (define KILPATRICK and undef REGIONAL) :

.. code-block:: console

   ...
   #define KILPATRICK      /* 2D sst front*/
   /*
        ... OR REALISTIC CONFIGURATIONS
   */
   #undef  COASTAL         /* COASTAL Applications */
   #undef  REGIONAL        /* REGIONAL Applications */
   ...

In param.h, you need to change the MPI setup by :

.. code-block:: fortran

   parameter (NP_XI=4,  NP_ETA=1,  NNODES=NP_XI*NP_ETA)
   
Contrary to the cold-to-warm case, you need to modify the SST front by multiplying by -1.0 the xr variable in the file ana_initial.F :

.. code-block:: fortran
  #    ifdef TEMPERATURE
                t(i,j,k,1,itemp)=(288.95-273.16)+1.5*TANH(-1.0*xr(i,j)/100.E+3)
                t(i,j,k,2,itemp)=(288.95-273.16)+1.5*TANH(-1.0*xr(i,j)/100.E+3)
  #    endif /* TEMPERATURE */

Simulation
----------

Here is an example of the croco.in file to use (same as the cold-to-warm case) :

.. code-block:: console

   title:
        KILPATRICK
   time_stepping: NTIMES   dt[sec]  NDTFAST  NINFO
                1296      100      2      1

   S-coord: THETA_S,   THETA_B,    Hc (m)
           7.0d0     2.0d0      200.0d0

   initial: NRREC / filename
          0
      croco_ini.nc
   restart:          NRST, NRPFRST / filename
                   2592    -1
      croco_rst.nc

   history: LDEFHIS, NWRT, NRPFHIS / filename
            T      36     0
      croco_his.nc
   averages: NTSAVG, NAVG, NRPFAVG / filename
            1      36     0
      croco_avg.nc
   primary_history_fields: zeta UBAR VBAR  U  V   wrtT(1:NT)
                          T    T   T   T  T    40*T
   auxiliary_history_fields:   rho Omega  W  Akv  Akt  Aks  Bvf  Visc3d Diff3d  HBL HBBL Bostr Bustr Bvstr Wstr Ustr Vstr Shfl Swfl rsw rlw lat sen HEL
                             F   F     T   F    T    F   F    F       F       T   T    T     F      F     T    T    T    T    T   30*T

   primary_averages: zeta UBAR VBAR  U  V   wrtT(1:NT)
                   T    T    T    T  T   40*T
   auxiliary_averages: rho Omega  W  Akv  Akt  Aks  Bvf Visc3d Diff3d HBL HBBL Bostr Bustr Bvstr Wstr Ustr Vstr Shfl Swfl rsw rlw lat sen HEL
                     F   T     T   F    T    F    F    F     F      T   T    T     F     F     T   T    T     T    T   30*T

   abl: ldefablhis, nwrtablhis, nrpfablhis / filename
              T       36     0
      croco_abl_his.nc
   abl_history_fields: pu_dta pv_dta pt_dta pq_dta pgu_dta pgv_dta u_abl v_abl t_abl q_abl tke mxlm mxld avm avt ablh zr zw Hzr Hzw
                      T      T       T      T       T      T     T      T     T     T     T   T    T    T   T   T   T  T  T   T
   abl_averages: ldefablavg, ntsablavg, nwrtablavg, nrpfablavg / filename
                  F        1             1          0
      croco_abl_avg.nc
   abl_averages_fields: pu_dta pv_dta pt_dta pq_dta pgu_dta pgv_dta u_abl v_abl t_abl q_abl tke mxlm mxld avm avt ablh zr zw Hzr Hzw
                      T      T       T      T       T      T     T      T      T     T     T   T    T    T   T   T   T  T  T   T

   rho0:
      1025.d0

   vertical_mixing: Akv_bak, Akt_bak [m^2/sec]
                   0.d0    30*0.d0

   bottom_drag:     RDRG [m/s],  RDRG2,  Zob [m],  Cdb_min, Cdb_max
                 3.0d-04      0.d-3    0.d-3     1.d-4    1.d-1

   gamma2:
                 1.d0

   abl_nudg_cof:    ltra_min, ltra_max, ldyn_min, ldyn_max  [seconds for all]
                      5400.     3600.     5400.    3600.

   nudg_cof:    TauT_in, TauT_out, TauM_in, TauM_out  [days for all]
                  1.      360.      3.      360.


Launch the simulation with mpirun (it takes less than 10 secondes) :

.. code-block:: console
   
   mpirun -np 4 ./croco

Results
-------

At the end of the simulation you should have the file croco_abl_his.nc. It contains instantanneous ABL1d variables.
