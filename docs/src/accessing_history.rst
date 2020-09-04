Accessing Validation History
============================


Any time you run **validate**, the comparison results are cached on the file system. 


There are a few functions that provide access to previous comparisons:


.. code-block:: python

    from greenlight import last_comparison, all_comparisons, all_comparisons_dict


**last_comparison(product_code)**
---------------------------------

.. code-block:: python

    Retrieve the last-known comparison for a given product.


This returns the last comparison that took place. 


This contains the **file-level comparisons**, **table-level comparisons**, and any **custom facts** set.



.. code-block:: python

    prev_results = last_comparison('ff.all') # That's it!



**all_comparisons(product_code)**
---------------------------------


Return a list of historical comparisons.


This returns a list of all of the known comparisons (from earliest to latest).


You can retrieve the same comparison results **by date/timestamp** by using **all_comparisons_dict**.


**all_comparisons_dict(product_code)**
--------------------------------------


Return a collection of historical comparisons as a dictionary.

Each key is the validation date/timestamp of the previous run.

.. code-block:: python

    >>> historical_comps = all_comparisons_dict('ff.all')

    >>> historical_comps.keys()
	[u'20181008_20_44',
	 u'20181010_12_59',
	 u'20181010_13_02',
	 u'20181010_16_01',
	 u'20181010_16_17',
	 u'20181010_16_31',
	 u'20181010_17_23',
	 u'20181010_17_27',
	 u'20181011_16_01',
	 u'20181011_16_30',
	 u'20181011_16_56',
	 u'20181011_17_14',
	 u'20181011_17_24',
	 u'20181012_15_36',
	 u'20181015_13_43',
	 u'20181015_13_48',
	 u'20181015_14_01']


This way you can retrieve specific results.


.. code-block:: python

    # You can pass this to test_run() 
    prev_result = historical_comps['20181010_16_31']



**test_run(prev_results, run_default_tests=True, custom_tests=None, log_dir=None)**
-----------------------------------------------------------------------------------

You can see how a given results set responds to tests by using the **test_run** function.

Simulate a validation run using a previously generated set of results.

This can help you see how the validation results would be affected by using different combinations of custom tests.

You can use ``log_dir`` to specify a custom log location. 


.. code-block:: python

    prev_result = last_comparison('ff.all')
    
    # See how validation would be affected by a set of new/different tests
    test_run(prev_result, custom_tests=[<some new tests specified here>])

    # See how validation would be affected by *no* custom tests.
    test_run(prev_result, custom_tests=[])

    test_run(
        prev_results, 
        custom_tests=[<some custom tests>,],
        log_dir='/home/wrds/josepd/tmp_log_dir', # Here I'm altering the location the log will be written to
    )


**test_last_comparison(product_code, run_default_tests=True, custom_tests=None, log_dir=None)**
-----------------------------------------------------------------------------------------------

This function is a convenience wrapper around ``test_run()`` + ``last_comparison()``.

This will:

* find the last comparison (automatically)
* run the specified tests against those results
* produce a log of the results
