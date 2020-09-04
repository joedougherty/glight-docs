Crafting Custom Tests 
=====================


One of the tricky bits about validation is that what is considered "valid" may change from dataset to dataset. It may even change for the same dataset over time.

As such, it is of little use to try to *guess* at what makes a given dataset "valid" or not. Flexibility **must** be a first-class feature, not 
something to be bolted on later.

While **greenlight** provides a handful of reasonable sanity checks, its true power is that it gives you programmatic access to the facts about
your dataset. This allows you to craft appropriate tests.

How might you go about this?


--------------------------------------------
A worked example: Dev vs. Prod Record Counts 
--------------------------------------------


Let's say you are currently working on FRB (**frb.all**). 


You have decided to add a test that ensures that each new table in dev contains *at least* as many records as its production counterpart.


Step 1: Inspect the collected facts for an appropriate test point.
------------------------------------------------------------------


.. code-block:: python

    from greenlight import collect_facts

    facts = collect_facts('frb.all')

    # What measures are already calculated?
    # Each table-level comparison is just a dictionary
    #
    # Let's take a look at the first compare to see if there's anything useful here...

    >> facts['table_level_comparisons'][0].keys()
     
    ['dev_rec_count',
     'dev_var_count',
     'prod_rec_count',
     'dev_var_list',
     'vars_in_prod_but_not_in_dev',
     'vars_identical_in_dev_and_prod',
     'prod_var_list',
     'vars_in_dev_but_not_in_prod',
     'dsn',
     'prod_var_count',
     'parent_dir']


Oh! I can use ``dev_rec_count`` and ``prod_rec_count`` here!


Step 2: Write a test.
---------------------

Here is a sample test.

.. code-block:: python

    def more_obs_in_dev_than_prod(results):
        failures = []
        for result in results['table_level_comparisons']:
            if result['prod_rec_count'] < result['dev_rec_count']:
                failures.append(result)

        return failures


Step 3: Test your test.
-----------------------


You already have the facts -- make sure your new function does what you think it does.

.. code-block:: python

    >> assert len(more_obs_in_dev_than_prod(facts)) == 0



Step 4: Run Validation including your new test.
-----------------------------------------------

.. code-block:: python

    >> validate('frb.all', custom_tests=[more_obs_in_dev_than_prod])


-----------------
Anatomy of a Test
-----------------

If you wish to write a test for any/all tables, iterate over the **results['table_level_comparisons']** list looking for tables that meet your given failure criteria.


.. code-block:: python

    def function_name_is_the_test_name(results):
        # set up a list to collect found failures
        failures = []

        # we'll take a look at every table comparison along the way
        for result in results['table_level_comparisons']:
            
            # check for the failure condition
            if <some condition(s)>:
                # add this comparison to our failures collection
                failures.append(result)

        # finally, pass back our collected failures to the caller
        return failures


This same idea applies to comparison results in **results['library_level_comparisons']**.


--------------------
Table-Specific Tests
--------------------

Specific tables can be found by **dsn**. 


.. code-block:: python

    def table_specific_test(results):
        failures = []

        for result in results['table_level_comparisons']:
            if result['dsn'] == <table name>: 
                if <some other condition(s)>:
                    failures.append(result)

        return failures


You could also write a test for multiple tables.


.. code-block:: python
    
    def test_a_few_tables_for_the_same_condition(results):
        failures = []

        tables_in_question = ('table1', 'table2', 'table3')

        for result in results['table_level_results']:
            if result['dsn'] in tables_in_question:
                if <some condition(s)>:
                    failures.append(result)

        return failures


----------------------
Tests for Custom Facts
----------------------


Custom Facts appear in the **results['custom_facts']** dictionary.


.. code-block:: python

    def custom_facts_test(results):
        result = results['custom_facts']
        if result['custom_fact_1'] < results['custom_fact_2']:
            return result
