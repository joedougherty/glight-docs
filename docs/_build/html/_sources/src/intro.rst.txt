Introduction 
============


**greenlight** is a lightweight dataset validation framework. 

It is used to ensure that a new data release is ready for publication to production. 

**greenlight** facilitates validation by running several default tests to compare the development and production datasets. In addition, **greenlight** gives the developer the power to author their own tests.

Overview
---------

**greenlight** operates as follows. It:

1.) Collects facts about your product at both the *library* level and the *table* level.

* **Library composition**
    * Which tables comprise a given library (dev and prod)?
* **Table composition**
    * Which variables comprise a given table (dev and prod)?
    * How many records does a table have (dev and prod)?

2.) Runs tests to determine if any of these results constitute a failure *for your specific product*.

* Default tests:
    * Do the *dev* and *prod* environment share the same tables?
    * For each table, do the *dev* and *prod* versions share the same variables?

3.) Produces a log of your test results.

4.) Returns **True** if all tests pass. Otherwise, it raises a ``ValidationError``.


You can specify:

* custom facts
* custom tests
* tables to exclude from validation
* tables to include (only!) for validation


Quickstart
----------

Generally, you start off a validation session using **validate.**


.. code-block:: python

    from greenlight import validate


    validate(
        'ff.all', # the product code in question
    )



You can specify custom tests:


.. code-block:: python

    # Custom tests
    custom_tests = [<include custom tests here>]

    validate(
        'ff.all',
        custom_tests=custom_tests,
    )
    

You can specify custom facts:


.. code-block:: python

    # Custom tests and facts
    custom_facts = [<custom facts declared here>]
    custom_tests = [<include custom tests here>]

    validate(
        'ff.all',
        custom_tests=custom_tests,
        custom_facts=custom_facts,
    )


You can exclude tables:


.. code-block:: python

    # Exclude certain tables
    exclude = ['portfolios', 'keys_report']

    validate(
        'ff.all',
        exclude=exclude,
    )


You can include only the tables you need:


.. code-block:: python

    # Include certain tables only
    include_only = ['portfolios']

    validate(
        'ff.all',
        include_only=include_only,
    )


You can specify a table's keys:

.. code-block:: python

    # Specify table-specific keys to use
    options = [
        {'dsn': 'factors_daily', 'path_to_lib': '/wrds/ff/sasdata', 'keys': ['smb', 'date']},
    ]

    validate(
        'ff.all',
        options=options,
    )

