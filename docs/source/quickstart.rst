Quickstart
==========

``intake_avro`` provides quick and easy access to tabular data stored in
the Apache `Avro`_ binary, columnar format.

.. _Avro: https://avro.apache.org/docs/current/

Installation
------------

To use this plugin for `intake`_, install with the following command::

   conda install -c intake intake-avro

.. _intake: https://github.com/ContinuumIO/intake

Usage
-----

Ad-hoc
~~~~~~

After installation, the functions ``intake.open_avro_table``
and ``intake.open_avro_sequence`` will become available. The former, faster
method, can be used to open one or more avro files with `flat` schema into data-frames, but
the latter can be used for any files and produces generic sequences of dictionaries.

Assuming some avro files in
a given path, the following would load them into a data-frame::

   import intake
   source = intake.open_avro('data_path/*.avro')
   dataframe = source.read()

There will be one data partition per input file, there is no random access
within each avro data file.

Arguments to ``open_avro``:

- ``urlpath`` : the location of the data. This can be a single file, a list of specific files,
 or a glob string (containing ``"*"``). The
 URLs can be local files or, if using a protocol specifier such as ``'s3://'``, a remote file
 location.

- ``storage_options`` : other parameters that are to be passed to the  file-system
 implementation, in the case that a remote file-system is referenced in ``urlpath``. For
 specifics, see the dask `documentation`_.

.. _documentation : http://dask.pydata.org/en/latest/remote-data-services.html

A source so defined will provide the usual methods such as ``discover`` and ``read_partition``.

Creating Catalog Entries
~~~~~~~~~~~~~~~~~~~~~~~~

To include in a catalog, the plugin must be listed in the plugins of the catalog::

   plugins:
     source:
       - module: intake_avro

and entries must specify ``driver: avro_table`` or ``driver: avro_sequence``.
The further arguments are exactly the same
as for the ``open_avro_*`` functions.

Using a Catalog
~~~~~~~~~~~~~~~

Assuming a catalog file called ``cat.yaml``, containing a avro source ``pdata``, one could
load it into a dataframe as follows::

   import intake
   cat = intake.Catalog('cat.yaml')
   df = cat.pdata.read()

The type of the output will depend on the plugin that was defined in the catalog. You can
inspect this before loading by looking at the ``.container`` attribute, which will be
either ``"dataframe"`` or ``"python"``.

The number of partitions will be equal to the number of files pointed to.