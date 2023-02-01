Run 2d idealized SST front test cases
=====================================

.. _installation:

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
