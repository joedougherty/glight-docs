Beyond Basic Usage: Specifying Table Keys
=============================================

In some instances you may want to specify your own keys for a given table. The below example demonstrates.


.. code-block:: python

    from greenlight import validate, collect_facts, Fact, Min_Date, SAS_Fact


    custom_facts = [
        # This gets added to the dictionary in results['custom_facts']:
        #   {'dev_smb_null_rec_count': <dev count>, 
        #    'prod_smb_null_rec_count': <prod count>}
        Fact('smb_null_rec_count', path_to_lib='/wrds/ff/sasdata', dsn='factors_daily', fact='count(*)', where='smb = .'),
        
        # This gets added to the dictionary in results['custom_facts']:
        #   {'dev_factors_monthly_min_date': <dev min date>,
        #    'prod_factors_monthly_min_date': <prod min date>}
        Min_Date('date', path_to_lib='/wrds/ff/sasdata', dsn='factors_monthly'),

        # This gets added to the dictionary in results['custom_facts']:
        #   {'custom_count': <custom count script result>}
        #
        # For example, we can expect this program to return 100.
        SAS_Fact('custom_count', '../examples/custom_count.sas')

        # Because result['custom_facts'] is a dictionary, it's important to use unique fact names.
        #
        # If you have two facts with the same name, the value in results['custom_facts'] will 
        # correspond to the last obtained fact of that name.
    ]


    def smb_null_recs_test(results):
        result = results['custom_facts']
        if result['dev_smb_null_rec_count'] > result['prod_smb_null_rec_count']:
            return result
        

    def factors_monthly_min_date_test(results):
        result = results['custom_facts']
        if result['dev_factors_monthly_min_date'] != '1927-09-01':
            return result


    def custom_fact_example_test(results):
        result = results['custom_facts']
        if result['custom_count'] != 100:
            return result


    custom_tests = [smb_null_recs_test, factors_monthly_min_date_test, custom_fact_example_test]

    options = [
        # Pass in which keys to use in the comparisons by key(s)
        {'dsn': 'factors_daily', # Specify table...
         'path_to_lib': '/wrds/ff/sasdata', # ... and directory
         'keys': ['date', 'smb']}
    ]


    validate(
        'ff.all', 
        custom_facts=custom_facts,
        custom_tests=custom_tests,
        options=options,
    )
