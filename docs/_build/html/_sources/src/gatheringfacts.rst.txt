------------------------
Manually Gathering Facts
------------------------


If you wish to inspect the fact collection that **greenlight** will run its tests against, use the **collect_facts** function.


.. code-block:: python

    from greenlight import collect_facts


    known_facts = collect_facts(
        'ff.all',
        custom_facts=custom_facts,              # you can specify gathering of custom facts 
        exclude=[<any files to exclude>],       # Optionally, exclude certain tables
        include_only=[<files to include only>], # Optionally, include only certain tables
        options=[<options here>],               # Specify table keys if desired
    )

