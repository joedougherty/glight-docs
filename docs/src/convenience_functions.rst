Convenience Functions
=====================


There are a handful of functions at your disposal for making quick interactive queries. 

These can be helpful during the fact/test development process.

Unless noted otherwise, these can all be imported from the top-level **greenlight** module.


.. code-block:: python

    from greenlight import get_variables, get_percent_null, get_rec_count # etc...


---------------------------------
**average_runtime(product_code)**
---------------------------------


    Returns the average (mean, non-weighted) duration for validation runs by product_code.

    Duration is provided in seconds.


    >>> average_runtime('ff.all')
    24.60149371623993


-----------------------------------
**get_variables(path_to_lib, dsn)**
-----------------------------------

    Returns a list of all the variables referenced by the table `dsn` in `path_to_lib`.


    >>> get_variables('/wrds/ff/sasdata', 'factors_monthly')
    set(['mktrf', 'month', 'hml', 'umd', 'rf', 'dateff', 'year', 'date', 'smb'])


------------------------------------------------
**get_percent_null(variable, path_to_lib, dsn)**
------------------------------------------------


    Returns percentage null by `variable` for `dsn` in `path_to_lib`.

    >>> get_percent_null('PREV_DATE', '/wrds/zacks/sasdata', 'rating_detail')
    15.474037693076328


-----------------------------------
**get_rec_count(path_to_lib, dsn)**
-----------------------------------

    Returns total number of records for `dsn` in `path_to_lib`.


    >>> get_rec_count('/wrds/bank/sasdata/holding', 'bhcf201706')
    4673

------------------------------
**get_keys(path_to_lib, dsn)**
------------------------------

    Returns a list of keys obtained by querying the dictionary.columns table for columns where:
        * sorted_by is not null
        * sorted_by > 0

    The empty list denotes that no keys could be found.

    >>> get_keys('/wrds/zacks/sasdata', 'eps_cons_weekly')
    ['zid', 'est_type', 'reference_period', 'observation_date']

-----------------------------------------------------
**get_counts_by_keys(path_to_lib, dsn, keys='NONE')**
-----------------------------------------------------

    Produce counts by keys. The following measures are calculated:
        * uniques_by_keys       :: # of unique records (keyed by provided keys)
        * duplicates_by_keys    :: # of duplicate records (keyed by provided keys)
        * missing_by_keys       :: # of missing records (keyed by provided keys)
        
    (Note: this will also return the keys used to produce these counts for reference.)

    >>> get_counts_by_keys('/wrds/ff/sasdata', 'factors_daily')
    {'duplicates_by_keys': 0,
     'keys': ['date'],
     'missing_by_keys': 0,
     'uniques_by_keys': 24265}

    You can specify custom keys by using the ``keys`` argument:

    >>> get_counts_by_keys('/wrds/ff/sasdata', 'factors_daily', keys=['date', 'smb'])
    {'duplicates_by_keys': 0,
     'keys': ['date', 'smb'],
     'missing_by_keys': 0,
     'uniques_by_keys': 24265}

    If you do not specify keys, appropriate keys will be obtained by querying the 
    dictionary.columns table for columns where:
    * sorted_by is not null
    * sorted_by > 0
    
-----------------------------------------------
**get_fact(path_to_lib, dsn, fact, where='1')**
-----------------------------------------------

    A wrapper function to enable convenient retrieval of certain types of SAS facts.

    The query can be thought of as:

.. code-block:: SAS

    libname current "<path_to_lib>";
    proc sql;
        select 
        <fact> 
        from current.<dsn>
        where <where_condition>;


    >>> get_fact('/wrds/ff/sasdata', 'factors_monthly', 'count(date)', where='smb = .')
    0

----------------------------------------
**get_null_variables(path_to_lib, dsn)**
----------------------------------------


    Determine which variables are entirely null (if any) for a given dataset.

    This returns a list of all variables that are composed entirely of missing values. 
    
    If all variables contain at least *some* data, the empty list will be returned.

    >>> get_null_variables('/wrds/ff/sasdata', 'factors_daily')
    []

----------------------------------
**get_encoding(path_to_lib, dsn)**
----------------------------------


    Determine the encoding of the given dataset.


    >>> get_encoding('/wrds/ff/sasdata', 'factors_daily')
    'latin1  Western (ISO)'
