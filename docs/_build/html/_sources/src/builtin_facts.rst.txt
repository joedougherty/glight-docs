Built-in Facts
==============


**greenlight** provides a few built-in facts.


**Percent_Null**
----------------

Find the percent null for a given variable in the provided dataset (in both dev and prod).

.. code-block:: python

    pn = Percent_Null(
        variable,       # variable in question (Note: this is case-sensitive)
        path_to_lib,    # directory (ex: "/wrds/ff/sasdata")
        dsn,            # dataset (ex: "factors_monthly")
    )


1.) Called directly -- good for verifying behavior.

.. code-block:: python

    >>> pn = Percent_Null('PRICE', '/wrds/levin/sasdata', 'healthcare_ma')
    >>> pn.obtain()
    {'dev_healthcare_ma_PRICE_percent_null': 0.39591,
     'prod_healthcare_ma_PRICE_percent_null': 0.39591}

2.) As a custom fact.

.. code-block:: python

	# Add the custom fact to your `custom_facts` list.
	custom_facts.append(Percent_Null('PRICE', '/wrds/levin/sasdata', 'healthcare_ma'))
    

**Min_Date** 
------------

Find the minimum date value for the provided variable in the provided dataset (in both dev and prod).

.. code-block:: python

    min_date = Min_Date(
        date_variable,              # date variable in question (Note: this is case-sensitive)
        path_to_lib,                # directory (ex: "/wrds/ff/sasdata")
        dsn,                        # dataset (ex: "factors_monthly")
        date_format='yymmdd10.',    # [Optional] date output format (yymmdd10. by default)
    )


1.) Called directly -- good for verifying behavior.

.. code-block:: python

    >>> min_date = Min_Date('date', '/wrds/ff/sasdata', 'factors_daily')
    >>> min_date.obtain()
    {'dev_factors_daily_min_date': '1926-07-01',
     'prod_factors_daily_min_date': '1926-07-01'}

2.) As a custom fact:


.. code-block:: python

    # Add the fact to your `custom_facts` list.
    custom_facts.append(
        Min_Date('date', '/wrds/ff/sasdata', 'factors_daily')
    )

Optionally, you can set the date format for your output.

.. code-block:: python

    >>> Min_Date('date', '/wrds/ff/sasdata', 'factors_daily', date_format='yymmdd8.')


**Max_Date**
------------

Find the maximum date value for the provided variable in the provided dataset (in both dev and prod).

.. code-block:: python

    max_date = Max_Date(
        date_variable,              # date variable in question (Note: this is case-sensitive)
        path_to_lib,                # directory (ex: "/wrds/ff/sasdata")
        dsn,                        # dataset (ex: "factors_monthly")
        date_format='yymmdd10.',    # [Optional] date output format (yymmdd10. by default)
    )


1.) Called directly -- good for verifying behavior.

.. code-block:: python

    >>> max_date = Max_Date('date', '/wrds/ff/sasdata', 'factors_daily')
    >>> max_date.obtain()
    {'dev_factors_daily_max_date': '2018-06-29',
     'prod_factors_daily_max_date': '2018-08-31'}

2.) As a custom fact:

.. code-block:: python

    # Add the fact to your `custom_facts` list.
    custom_facts.append(Max_Date('date', '/wrds/ff/sasdata', 'factors_daily'))

    Optionally, you can set the date format for your output.

    >>> Max_Date('date', '/wrds/ff/sasdata', 'factors_daily', date_format='yymmdd8.')


**Null_Variables**
------------------

Discover which variables in a given table are composed entirely of missing values.

1.) Called directly -- good for verifying behavior.

.. code-block:: python

    >>> null_vars = Null_Variables('/wrds/ff/sasdata', 'factors_monthly')
    >>> null_vars.obtain()
    {'dev_factors_daily_null_variables': [],
     'prod_factors_daily_null_variables': []}

2.) As a custom fact.

.. code-block:: python

    # Add the custom fact to your `custom_facts` list.
    custom_facts.append(Null_Variable('/wrds/ff/sasdata', 'factors_monthly'))
