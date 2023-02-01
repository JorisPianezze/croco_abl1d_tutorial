New features
============

.. _ref_get_code

Get the code
------------

To install CROCO with ABL1d developments, get the dev_2022_ABL1d branch from INRIA's gitlab server :

.. code-block:: console

   git clone https://gitlab.inria.fr/croco-ocean/croco.git
   git checkout -b dev_2022_ABL1d origin/dev_2022_ABL1d 

Code modifications
------------------

All ABL1d developpments are made under the CPP key ABL1D.

croco.in
--------

Additionnal parameters have been include in :file:`croco.in` file :

* to define the ABL1d output :

.. code-block:: fortran

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

* to define the ABL1d nudging coefficients :

.. code-block:: fortran

   abl_nudg_cof:    ltra_min, ltra_max, ldyn_min, ldyn_max  [seconds for all]
                   5400.     3600.     5400.    3600.

