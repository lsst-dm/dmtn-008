..
  Content of technical report.

  See http://docs.lsst.codes/en/latest/development/docs/rst_styleguide.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label
     :target: http://target.link/url

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-report-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 2

*Draft in Development*

Examples here assume tickets/DM-5120 (which is not yet merged into master).  This ticket gives ``validateDrp.py`` the ability to work on an arbitrary repository with no need for a configuration file.

Executive Summary
=================

.. code-block:: bash

    validateDrp.py Cfht/output
    [...]
    PA1(RMS) = 19.07+-0.00 mmag
    PA1(IQR) = 20.86+-0.40 mmag
    minimum: PF1=20% of diffs more than PA2 = 24.14 mmag (target is < 15 mmag)
    design : PF1=10% of diffs more than PA2 = 30.27 mmag (target is < 15 mmag)
    stretch: PF1= 5% of diffs more than PA2 = 36.26 mmag (target is < 10 mmag)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [4.00, 6.00] arcmin, AM1=9.23 mas (target is < 10 mas).
      10.46% of sample deviates by >30 mas (target is < 10%)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [19.00, 21.00] arcmin, AM2=7.21 mas (target is < 10 mas).
      11.32% of sample deviates by >30 mas (target is < 10%)

Calculates, prints, plots, and saves validation metrics for the calibrated data in that repository.

For more details on the package itself and installation, see 
https://github.com/lsst/validate_drp

Sample Data
===========

While ``validate_drp`` will run on any data set, two sets of data from instruments similar to LSST have been created to provide end-to-end examples of producing an output data repository and analyzing it with ``validate_drp``.  These have also proven to be useful for debugging and checking the results of algorithmic and configuration changes in the pipeline code.

1. ``validation_data_cfht`` contains test CFHT data and selected SDSS reference catalogs in astrometry.net format.
2. ``validation_data_decam`` contains test DECam data and selected SDSS reference catalogs in astrometry.net format.


Examples
========

Run on sample CFHT data
-----------------------

.. code-block:: bash
    :name: cfht-runexample

    setup obs_cfht
    setup validation_data_cfht
    setup validate_drp
    ${VALIDATE_DRP_DIR}/examples/runCfhtTest.sh

produces an output repository and analyzes it with ``validateDrp.py Cfht/output``.

.. code-block:: bash
    :name: run-cfht-output

    [...]
    Median value of the astrometric scatter - all magnitudes: 63.159 mas
    Astrometric scatter (median) - mag < 21.0 : 11.4 mas
    Median value of the photometric scatter - all magnitudes: 118.271 mmag
    Photometric scatter (median) - mag < 21.0 : 14.3 mmag
    No stars found that are 199.0--201.0 arcmin apart.
    PA1(RMS) = 19.07+-0.00 mmag
    PA1(IQR) = 20.86+-0.40 mmag
    minimum: PF1=20% of diffs more than PA2 = 24.14 mmag (target is < 15 mmag)
    design : PF1=10% of diffs more than PA2 = 30.27 mmag (target is < 15 mmag)
    stretch: PF1= 5% of diffs more than PA2 = 36.26 mmag (target is < 10 mmag)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [4.00, 6.00] arcmin, AM1=9.23 mas (target is < 10 mas).
      10.46% of sample deviates by >30 mas (target is < 10%)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [19.00, 21.00] arcmin, AM2=7.21 mas (target is < 10 mas).
      11.32% of sample deviates by >30 mas (target is < 10%)

.. figure:: /_static/Cfht_output_r_check_astrometry.png
    :name: fig-cfht-pa1
    :alt: CFHT Astrometry RMS
    :target: ../..//_static/Cfht_output_r_check_astrometry.png

    Distribution of the RMS in the measured position of the sources for the same object across visits.

.. figure:: /_static/Cfht_output_r_check_photometry.png
    :name: fig-cfht-pa1
    :alt: CFHT Photometry RMS
    :target: ../../_static/Cfht_output_r_check_photometry.png

    The photometric repeatability in the measured magnitude of the sources for the same object across visits.
    Based on ``base_PsfFlux_flux`` as calibrated using ``calexp.calib``.
    The blue subsample indicates stars < 21 mag.

    (top left) Distribution of RMS for each object.
    (top right) RMS vs. mean magnitude.
    (bottom left) A comparison of the quoted uncertainty vs. the observe variation (log-log scale in mmag).  Quoted magnitude uncertainty from the ``src`` file.  RMS of quoted magnitude.
    (bottom right) Quoted magnitude uncertainty vs. mean magnitude.  Fit model for expected behavior for photon Poisson statistics.
    
These first two figures aren't actually formal SRD numbers.  They are instead the same-object repeatability RMS in the calibrated values of the astrometric position and magnitude.
    
The next three figures are representations of the formal LSST SRD numbers.

.. figure:: /_static/Cfht_output_r_PA1.png
    :name: fig-cfht-pa1
    :alt: CFHT PA1
    :target: ../../_static/Cfht_output_r_PA1.png


    Difference in magnitude between the visits for the stars between 17--21.5 mag.  In this example there are only two visits.  In general ``validate_drp`` considers a random sample of pairs of visits.
    The RMS and the inter-quartile range (IQR: 75%-25%) are each computed from the distribution of these differences in magnitudes.   The reported IQR is normalized to the same scale as an RMS.
    (left) Difference vs. mean magnitude.  RMS (red), IQR (green) are shown as horizontal lines.
    (right) Distribution of the idfferences.  The RMS (ref) and IQR (green) are visualized as Gaussians with those values.

.. figure:: /_static/Cfht_output_r_AM1_D_5_ARCMIN_17.0-21.5.png
    :name: fig-cfht-am1
    :alt: CFHT AM1
    :target: ../../_static/Cfht_output_r_AM1_D_5_ARCMIN_17.0-21.5.png

    Distribution of the repeatability of astrometric distance between stars separted by 4-6 arcminutes.  The SRD spec of AM1=10 mas is shown in red.  The SRD spec for the outlier fraction with RMS relative separation of > AM1+AD1=30 mas (green) is 10.46%, which is only slight above the "design" specification of AF1=10%.

.. figure:: /_static/Cfht_output_r_AM2_D_20_ARCMIN_17.0-21.5.png
    :name: fig-cfht-am2
    :alt: CFHT AM2
    :target: ../../_static/Cfht_output_r_AM2_D_20_ARCMIN_17.0-21.5.png

    Distribution of the repeatability of astrometric distance between stars separted by 19-21 arcminutes.  AM2.


Run on sample DECam data
------------------------

.. code-block:: bash
    setup obs_decam
    setup validation_data_decam
    setup validate_drp
    ${VALIDATE_DRP_DIR}/examples/runDecamTest.sh

produces an output repository and analyzes it with ``validateDrp.py Decam/output``.

.. code-block:: bash
    :name: run-cfht-output

    [...]
    Median value of the astrometric scatter - all magnitudes: 70.671 mas
    Astrometric scatter (median) - mag < 21.0 : 35.1 mas
    Median astrometric scatter 35.1 mas is larger than reference : 25.0 mas
    Number of matched sources 8135 is too small (shoud be > 10000)
    Median value of the photometric scatter - all magnitudes: 81.483 mmag
    Photometric scatter (median) - mag < 21.0 : 65.6 mmag
    Median photometric scatter 65.629 mmag is larger than reference : 25.000 mag
    Number of matched sources 8135 is too small (shoud be > 10000)
    No stars found that are 199.0--201.0 arcmin apart.
    PA1(RMS) = 40.15+-0.00 mmag
    PA1(IQR) = 38.17+-0.48 mmag
    minimum: PF1=20% of diffs more than PA2 = 46.62 mmag (target is < 15 mmag)
    design : PF1=10% of diffs more than PA2 = 62.89 mmag (target is < 15 mmag)
    stretch: PF1= 5% of diffs more than PA2 = 79.46 mmag (target is < 10 mmag)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [4.00, 6.00] arcmin, AM1=29.15 mas (target is < 10 mas).
      48.77% of sample deviates by >30 mas (target is < 10%)
    Median of distribution of RMS of distance of stellar pairs.
    DESIGN goals
    For stars from 17.00 < mag < 21.50
    from D = [19.00, 21.00] arcmin, AM2=28.79 mas (target is < 10 mas).
      48.41% of sample deviates by >30 mas (target is < 10%)


.. figure:: /_static/Decam_output_z_check_astrometry.png
    :name: fig-cfht-pa1
    :alt: DECam Astrometry RMS
    :target: ../..//_static/Decam_output_z_check_astrometry.png

    Distribution of the RMS in the measured position of the sources for the same object across visits.

.. figure:: /_static/Decam_output_z_check_photometry.png
    :name: fig-cfht-pa1
    :alt: DECam Photometry RMS
    :target: ../../_static/Decam_output_z_check_photometry.png

    The photometric repeatability in the measured magnitude of the sources for the same object across visits.
    Based on ``base_PsfFlux_flux`` as calibrated using ``calexp.calib``.
    The blue subsample indicates stars < 21 mag.

    (top left) Distribution of RMS for each object.
    (top right) RMS vs. mean magnitude.
    (bottom left) A comparison of the quoted uncertainty vs. the observe variation (log-log scale in mmag).  Quoted magnitude uncertainty from the ``src`` file.  RMS of quoted magnitude.
    (bottom right) Quoted magnitude uncertainty vs. mean magnitude.  Fit model for expected behavior for photon Poisson statistics.
    
These first two figures aren't actually formal SRD numbers.  They are instead the same-object repeatability RMS in the calibrated values of the astrometric position and magnitude.
    
The next three figures are representations of the formal LSST SRD numbers.

.. figure:: /_static/Decam_output_z_PA1.png
    :name: fig-cfht-pa1
    :alt: DECam PA1
    :target: ../../_static/Decam_output_z_PA1.png


    Difference in magnitude between the visits for the stars between 17--21.5 mag.  In this example there are only two visits.  In general ``validate_drp`` considers a random sample of pairs of visits.
    The RMS and the inter-quartile range (IQR: 75%-25%) are each computed from the distribution of these differences in magnitudes.   The reported IQR is normalized to the same scale as an RMS.
    (left) Difference vs. mean magnitude.  RMS (red), IQR (green) are shown as horizontal lines.
    (right) Distribution of the idfferences.  The RMS (ref) and IQR (green) are visualized as Gaussians with those values.

.. figure:: /_static/Decam_output_z_AM1_D_5_ARCMIN_17.0-21.5.png
    :name: fig-cfht-am1
    :alt: DECam AM1
    :target: ../../_static/Decam_output_z_AM1_D_5_ARCMIN_17.0-21.5.png

    Distribution of the repeatability of astrometric distance between stars separted by 4-6 arcminutes.  The SRD spec of AM1=10 mas is shown in red.  The SRD spec for the outlier fraction with RMS relative separation of > AM1+AD1=30 mas (green) is 10.46%, which is only slight above the "design" specification of AF1=10%.

.. figure:: /_static/Decam_output_z_AM2_D_20_ARCMIN_17.0-21.5.png
    :name: fig-cfht-am2
    :alt: DECam AM2
    :target: ../../_static/Decam_output_z_AM2_D_20_ARCMIN_17.0-21.5.png

    Distribution of the repeatability of astrometric distance between stars separted by 19-21 arcminutes.  AM2.
