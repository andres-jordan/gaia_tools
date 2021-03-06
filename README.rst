gaia_tools
-----------

Tools for working with the `ESA/Gaia <http://sci.esa.int/gaia/>`__
data and related data sets (`APOGEE
<http://www.sdss.org/surveys/apogee/>`__, `GALAH
<https://galah-survey.org/>`__, and `RAVE
<https://www.rave-survey.org/project/>`__).

.. contents::

AUTHORS
========

 * Jo Bovy - bovy at astro dot utoronto dot ca
 * You!

Modified by Andrés Jordán eliminating some functionalities so that it is easier to install for students of my course (Astrofísica Experimental)

ACKNOWLEDGING USE OF THIS CODE
==============================

Please refer back to the original repository by Jo Bovy when using this code.

INSTALLATION
============

Standard python setup.py build/install

Either

``sudo python setup.py install``

or 

``python setup.py install --prefix=/some/directory/``

DEPENDENCIES AND PYTHON VERSIONS
=================================

This package requires `NumPy <http://www.numpy.org/>`__ and `astropy
<http://www.astropy.org/>`__. Some functions require `Scipy
<http://www.scipy.org/>`__ and the `apogee
<https://github.com/jobovy/apogee>`__ package. 

This package requires `fitsio <https://github.com/esheldon/fitsio>`

This package should work in both python 2 and 3. 

DATA FILES AND ENVIRONMENT VARIABLES
=====================================

This code will download and store various data files. The top-level
location of where these are stored is set by the **GAIA_TOOLS_DATA**
environment variable, which is the path of the top-level directory
under which the data will be stored. 

BASIC USE
==========

Catalog reading and cross-matching
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The basic use of the code is to read various data files and match them
to each other. For example, to load the `TGAS <http://www.cosmos.esa.int/web/gaia/iow_20150115>`__ data, do::

    import gaia_tools.load as gload
    tgas_cat= gload.tgas()

The first time you use this function, it will download the TGAS data
and return the catalog (the data is stored locally in a manner that
mirrors the Gaia archive, so downloading only happens once).

Similarly, to get data for the `GALAH <https://galah-survey.org/>`__
survey's DR1, do::

    galah_cat= gload.galah()


Similarly, you can load the `RAVE
<https://www.rave-survey.org/project/>`__ and `RAVE-on
<https://zenodo.org/record/154381#.V-D27pN97ox>`__ data as::

	rave_cat= gload.rave()
	raveon_cat= gload.raveon()

To match catalogs to each other, use the tools in
``gaia_tools.xmatch``. For example, to match the GALAH and APOGEE-RC
catalogs loaded above and compare the effective temperatures for the
stars in common, you can do::

	 from gaia_tools import xmatch
	 m1,m2,sep= xmatch.xmatch(rc_cat,galah_cat,colDec2='dec')
	 print(rc_cat[m1]['TEFF']-galah_cat[m2]['Teff'])
	      Teff     
	      K       
	 --------------
	 -12.3999023438
	  0.39990234375

which matches objects using their celestial coordinates using the
default maximum separation of 2 arcsec. To match catalogs with
coordinates at epoch 2000.0 to the TGAS data, which is at epoch 2015.,
give the ``epoch1`` and ``epoch2`` keyword. For example, to
cross-match the APOGEE-RC data and TGAS do::

	    tgas= gload.tgas()
	    aprc= gload.apogeerc()
	    m1,m2,sep= xmatch.xmatch(aprc,tgas,colRA2='ra',colDec2='dec',epoch2=2015.)
	    aprc= aprc[m1]
	    tgas= tgas[m2]


Further, it is possible to cross-match any catalog to the catalogs in
the CDS database using the `CDS cross-matching service
<http://cdsxmatch.u-strasbg.fr/xmatch>`__. For example, to match the
GALAH catalog to the Tycho-2 catalog, do the following::

   tyc2_matches, matches_indx= xmatch.cds(galah_cat,colDec='dec',xcat='vizier:Tycho2')
   print(galah_cat['RA'][matches_indx[0]],tyc2_matches['RA_1'][0],tyc2_matches['pmRA'][matches_indx[0]],tyc2_matches['pmDE'][matches_indx[0]])
   ('209.8838244', 209.88408100000001, -23.100000000000001, -10.699999999999999)

Let's see how these proper motions hold up in Gaia DR1! If you want to
download a catalog from CDS, you can use
``gaia_tools.load.download.vizier``.


API
====

(May or may not be fully up-to-date)

 * ``gaia_tools.load``
     * ``gaia_tools.load.galah``
     * ``gaia_tools.load.rave``
     * ``gaia_tools.load.raveon``
         * ``gaia_tools.load.download.vizier``
 * ``gaia_tools.xmatch``
     * ``gaia_tools.xmatch.xmatch``
     * ``gaia_tools.xmatch.cds``
     * ``gaia_tools.xmatch.cds_matchback``
