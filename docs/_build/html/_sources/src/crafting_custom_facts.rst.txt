Crafting Custom Facts
=====================


**greenlight** allows you to provide your own facts about your datasets.

--------------------
Using the Fact class
--------------------


A **Fact** class is provided for you to obtain simple facts via a SAS query.

Let's say we want a custom record count for one of our tables. 


**Example:** Custom Count using **Fact**


.. code-block:: python

    from greenlight import Fact

    # Step 1.) Declare the fact.
    #
    # Here we're asking how many records in ff.factors_daily have smb set to null.
    custom_count = Fact(
        'smb_null_count', 
        'count(*)', 
        path_to_lib='/wrds/ff/sasdata', 
        dsn='factors_daily', 
        where='smb = .'
    )
    
    # You can also use keyword arguments to clarify intent. 
    custom_count = Fact(
        factname='smb_null_count', 
        fact='count(*)', 
        path_to_lib='/wrds/ff/sasdata', 
        dsn='factors_daily',
        where='smb = .'
    )
    
    # Step 2.) Obtain the fact
    custom_count.obtain()
    {
        'dev_smb_null_count': 0,
        'prod_smb_null_count': 0,
    }


**Fact** returns a dictionary (keyed by the provided `factname`) and the fact test result as applied to both dev and prod.

----------------------------------------------
Using the **SAS_Fact** class to run custom SAS
----------------------------------------------


A **SAS_Fact** class is provided to allow you to collect results from you custom SAS scripts.

Let's you've written a script that does some analysis like so:

.. code-block:: rst

	proc sql noprint;
            create table analysis as
            select distinct a.value, a.measure
            from dictionary.tables a
            join sometable b
            on a.value = b.measure
            where <some condition holds>;

            select count(*) from analysis into :output_value; 


How can you use this with **SAS_Fact?**

Step 1: Include the **stdout** macro.
-------------------------------------

``%include "/wrds/lib/utility/stdout.sas";``

Step 2: Output the value in question using **%stdout()**.
---------------------------------------------------------

Our altered SAS script now looks like:

	
.. code-block:: rst

	%include "/wrds/lib/utility/stdout.sas";

	proc sql noprint;
            create table analysis as
            select distinct a.value, a.measure
            from dictionary.tables a
            join sometable b
            on a.value = b.measure
            where <some condition holds>;

            select count(*) from analysis into :output_value; 

	%stdout(&output_value);

Now you can use **SAS_Fact** to obtain this value during fact gathering.

First, test interactively.

.. code-block:: python

    from greenlight import SAS_Fact

    my_custom_fact = SAS_Fact(
        'my_custom_fact_key', 
        '/wrds/some_product/utilities/custom_fact.sas',
    )
    
    my_custom_fact.obtain()
    {'my_custom_fact_key': <result from SAS>}


    # You can also specify an output type for the SAS-derived value.
    #
    # Use Python's built-in types: int, float, str...
    my_custom_fact = SAS_Fact(
        'my_custom_fact_key', 
        '/wrds/some_product/utilities/custom_fact.sas', 
        output_type=int
    )

Once you are satisifed, add it to your custom_facts list.

.. code-block:: python

    custom_facts.append(
        SAS_Fact(
            'my_custom_fact_key', 
            '/wrds/some_product/utilities/custom_fact.sas',
        )
    )

Custom facts show up in the **collected_facts** dictionary and are keyed by **custom_facts**.

The **custom_facts** item is a dictionary. Custom facts will be keyed by the provided **factname**. This is true of instances of **Fact** as well as **SAS_Fact**.

--------------------------------------------------------
Passing data into **SAS_Fact** at Runtime (via initstmt)
--------------------------------------------------------


You can pass data into your custom SAS script at runtime. You can pass along macro variables as key/value pairs when declaring **SAS_Fact**. 


.. code-block:: python

    from greenlight import SAS_Fact

    #
    # This gets passed to SAS as "%let var1=123456;"
    #
    >>> f = SAS_Fact('demo_fact', 'initstmt_example.sas', var1=123456)

    >>> f.obtain()
    {'demo_fact': 123456}

