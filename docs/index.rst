.. Greenlight documentation master file, created by
   sphinx-quickstart on Thu Sep  3 13:29:18 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Greenlight's documentation!
======================================

.. toctree::
   :maxdepth: 2
   :hidden:

   src/overview.rst

.. toctree::
   :caption: Facts:
   :hidden:


   src/builtin_facts.rst
   src/crafting_custom_facts.rst
   src/gatheringfacts.rst

.. toctree::
   :caption: Tests:
   :hidden:


   src/crafting_custom_tests.rst

.. toctree::
   :caption: Examples:
   :hidden:


   src/basicexample.rst
   src/advexample.rst
   src/accessing_history.rst

.. toctree::
   :caption: Reference:
   :hidden:


   src/convenience_functions.rst


Introduction 
============


**greenlight** is a lightweight dataset validation framework. 

It is used to ensure that a new data release is ready for publication to production. 

**greenlight** facilitates validation by running several default tests to compare the development and production datasets. In addition, **greenlight** gives the developer the power to author their own tests.


.. include:: src/intro.rst  
