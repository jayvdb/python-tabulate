===============
python-tabulate
===============

Pretty-print tabular data in Python, a library and a command-line
utility.

The main use cases of the library are:

* printing small tables without hassle: just one function call,
  formatting is guided by the data itself

* authoring tabular data for lightweight plain-text markup: multiple
  output formats suitable for further editing or transformation

* readable presentation of mixed textual and numeric data: smart
  column alignment, configurable number formatting, alignment by a
  decimal point


Installation
------------

To install the Python library and the command line utility, run::

    pip install tabulate

The command line utility will be installed as ``tabulate`` to ``bin`` on Linux
(e.g. ``/usr/bin``); or as ``tabulate.exe`` to ``Scripts`` in your Python
installation on Windows (e.g. ``C:\Python27\Scripts\tabulate.exe``).

You may consider installing the library only for the current user::

    pip install tabulate --user

In this case the command line utility will be installed to ``~/.local/bin/tabulate``
on Linux and to ``%APPDATA%\Python\Scripts\tabulate.exe`` on Windows.

To install just the library on Unix-like operating systems::

    TABULATE_INSTALL=lib-only pip install tabulate

On Windows::

    set TABULATE_INSTALL=lib-only
    pip install tabulate


Build status
------------

.. image:: https://circleci.com/bb/astanin/python-tabulate.svg?style=svg
    :alt: Build status
    :target: https://circleci.com/bb/astanin/python-tabulate/tree/master


Library usage
-------------

The module provides just one function, ``tabulate``, which takes a
list of lists or another tabular data type as the first argument,
and outputs a nicely formatted plain-text table::

    >>> from tabulate import tabulate

    >>> table = [["Sun",696000,1989100000],["Earth",6371,5973.6],
    ...          ["Moon",1737,73.5],["Mars",3390,641.85]]
    >>> print(tabulate(table))
    -----  ------  -------------
    Sun    696000     1.9891e+09
    Earth    6371  5973.6
    Moon     1737    73.5
    Mars     3390   641.85
    -----  ------  -------------

The following tabular data types are supported:

* list of lists or another iterable of iterables
* list or another iterable of dicts (keys as columns)
* dict of iterables (keys as columns)
* two-dimensional NumPy array
* NumPy record arrays (names as columns)
* pandas.DataFrame

Examples in this file use Python2. Tabulate supports Python3 too.


Headers
~~~~~~~

The second optional argument named ``headers`` defines a list of
column headers to be used::

    >>> print(tabulate(table, headers=["Planet","R (km)", "mass (x 10^29 kg)"]))
    Planet      R (km)    mass (x 10^29 kg)
    --------  --------  -------------------
    Sun         696000           1.9891e+09
    Earth         6371        5973.6
    Moon          1737          73.5
    Mars          3390         641.85

If ``headers="firstrow"``, then the first row of data is used::

    >>> print(tabulate([["Name","Age"],["Alice",24],["Bob",19]],
    ...                headers="firstrow"))
    Name      Age
    ------  -----
    Alice      24
    Bob        19


If ``headers="keys"``, then the keys of a dictionary/dataframe, or
column indices are used. It also works for NumPy record arrays and
lists of dictionaries or named tuples::

    >>> print(tabulate({"Name": ["Alice", "Bob"],
    ...                 "Age": [24, 19]}, headers="keys"))
      Age  Name
    -----  ------
       24  Alice
       19  Bob


Row Indices
~~~~~~~~~~~

By default, only pandas.DataFrame tables have an additional column
called row index. To add a similar column to any other type of table,
pass ``showindex="always"`` or ``showindex=True`` argument to
``tabulate()``. To suppress row indices for all types of data, pass
``showindex="never"`` or ``showindex=False``.  To add a custom row
index column, pass ``showindex=rowIDs``, where ``rowIDs`` is some
iterable::

    >>> print(tabulate([["F",24],["M",19]], showindex="always"))
    -  -  --
    0  F  24
    1  M  19
    -  -  --


Table format
~~~~~~~~~~~~

There is more than one way to format a table in plain text.
The third optional argument named ``tablefmt`` defines
how the table is formatted.

Supported table formats are:

- "plain"
- "simple"
- "github"
- "grid"
- "fancy_grid"
- "pipe"
- "orgtbl"
- "jira"
- "presto"
- "psql"
- "rst"
- "mediawiki"
- "moinmoin"
- "youtrack"
- "html"
- "latex"
- "latex_raw"
- "latex_booktabs"
- "textile"

``plain`` tables do not use any pseudo-graphics to draw lines::

    >>> table = [["spam",42],["eggs",451],["bacon",0]]
    >>> headers = ["item", "qty"]
    >>> print(tabulate(table, headers, tablefmt="plain"))
    item      qty
    spam       42
    eggs      451
    bacon       0

``simple`` is the default format (the default may change in future
versions).  It corresponds to ``simple_tables`` in `Pandoc Markdown
extensions`_::

    >>> print(tabulate(table, headers, tablefmt="simple"))
    item      qty
    ------  -----
    spam       42
    eggs      451
    bacon       0

``github`` follows the conventions of `Github flavored Markdown`.  It
corresponds to the ``pipe`` format without alignment colons::

    >>> print(tabulate(table, headers, tablefmt="github"))
    | item   | qty   |
    |--------|-------|
    | spam   | 42    |
    | eggs   | 451   |
    | bacon  | 0     |


``grid`` is like tables formatted by Emacs' `table.el`_
package.  It corresponds to ``grid_tables`` in Pandoc Markdown
extensions::

    >>> print(tabulate(table, headers, tablefmt="grid"))
    +--------+-------+
    | item   |   qty |
    +========+=======+
    | spam   |    42 |
    +--------+-------+
    | eggs   |   451 |
    +--------+-------+
    | bacon  |     0 |
    +--------+-------+

``fancy_grid`` draws a grid using box-drawing characters::

    >>> print(tabulate(table, headers, tablefmt="fancy_grid"))
    ╒════════╤═══════╕
    │ item   │   qty │
    ╞════════╪═══════╡
    │ spam   │    42 │
    ├────────┼───────┤
    │ eggs   │   451 │
    ├────────┼───────┤
    │ bacon  │     0 │
    ╘════════╧═══════╛

``presto`` is like tables formatted by Presto cli::

    >>> print(tabulate(table, headers, tablefmt="presto"))
     item   |   qty
    --------+-------
     spam   |    42
     eggs   |   451
     bacon  |     0

``psql`` is like tables formatted by Postgres' psql cli::

    >>> print(tabulate(table, headers, tablefmt="psql"))
    +--------+-------+
    | item   |   qty |
    |--------+-------|
    | spam   |    42 |
    | eggs   |   451 |
    | bacon  |     0 |
    +--------+-------+

``pipe`` follows the conventions of `PHP Markdown Extra`_ extension.  It
corresponds to ``pipe_tables`` in Pandoc. This format uses colons to
indicate column alignment::

    >>> print(tabulate(table, headers, tablefmt="pipe"))
    | item   |   qty |
    |:-------|------:|
    | spam   |    42 |
    | eggs   |   451 |
    | bacon  |     0 |

``orgtbl`` follows the conventions of Emacs `org-mode`_, and is editable
also in the minor `orgtbl-mode`. Hence its name::

    >>> print(tabulate(table, headers, tablefmt="orgtbl"))
    | item   |   qty |
    |--------+-------|
    | spam   |    42 |
    | eggs   |   451 |
    | bacon  |     0 |

``jira`` follows the conventions of Atlassian Jira markup language::

    >>> print(tabulate(table, headers, tablefmt="jira"))
    || item   ||   qty ||
    | spam   |    42 |
    | eggs   |   451 |
    | bacon  |     0 |

``rst`` formats data like a simple table of the `reStructuredText`_ format::

    >>> print(tabulate(table, headers, tablefmt="rst"))
    ======  =====
    item      qty
    ======  =====
    spam       42
    eggs      451
    bacon       0
    ======  =====

``mediawiki`` format produces a table markup used in `Wikipedia`_ and on
other MediaWiki-based sites::

    >>> print(tabulate(table, headers, tablefmt="mediawiki"))
    {| class="wikitable" style="text-align: left;"
    |+ <!-- caption -->
    |-
    ! item   !! align="right"|   qty
    |-
    | spam   || align="right"|    42
    |-
    | eggs   || align="right"|   451
    |-
    | bacon  || align="right"|     0
    |}

``moinmoin`` format produces a table markup used in `MoinMoin`_
wikis::

    >>> print(tabulate(table, headers, tablefmt="moinmoin"))
    || ''' item   ''' || ''' quantity   ''' ||
    ||  spam    ||  41.999      ||
    ||  eggs    ||  451         ||
    ||  bacon   ||              ||

``youtrack`` format produces a table markup used in Youtrack
tickets::

    >>> print(tabulate(table, headers, tablefmt="youtrack"))
    ||  item    ||  quantity   ||
    |   spam    |  41.999      |
    |   eggs    |  451         |
    |   bacon   |              |

``textile`` format produces a table markup used in `Textile`_ format::

    >>> print(tabulate(table, headers, tablefmt="textile"))
    |_.  item   |_.   qty |
    |<. spam    |>.    42 |
    |<. eggs    |>.   451 |
    |<. bacon   |>.     0 |

``html`` produces standard HTML markup::

    >>> print(tabulate(table, headers, tablefmt="html"))
    <table>
    <tbody>
    <tr><th>item  </th><th style="text-align: right;">  qty</th></tr>
    <tr><td>spam  </td><td style="text-align: right;">   42</td></tr>
    <tr><td>eggs  </td><td style="text-align: right;">  451</td></tr>
    <tr><td>bacon </td><td style="text-align: right;">    0</td></tr>
    </tbody>
    </table>

``latex`` format creates a ``tabular`` environment for LaTeX markup,
replacing special characters like ``_`` or ``\`` to their LaTeX
correspondents::

    >>> print(tabulate(table, headers, tablefmt="latex"))
    \begin{tabular}{lr}
    \hline
     item   &   qty \\
    \hline
     spam   &    42 \\
     eggs   &   451 \\
     bacon  &     0 \\
    \hline
    \end{tabular}

``latex_raw`` behaves like ``latex`` but does not escape LaTeX commands
and special characters.

``latex_booktabs`` creates a ``tabular`` environment for LaTeX markup
using spacing and style from the ``booktabs`` package.


.. _Pandoc Markdown extensions: http://johnmacfarlane.net/pandoc/README.html#tables
.. _PHP Markdown Extra: http://michelf.ca/projects/php-markdown/extra/#table
.. _table.el: http://table.sourceforge.net/
.. _org-mode: http://orgmode.org/manual/Tables.html
.. _reStructuredText: http://docutils.sourceforge.net/docs/user/rst/quickref.html#tables
.. _Textile: http://redcloth.org/hobix.com/textile/
.. _Wikipedia: http://www.mediawiki.org/wiki/Help:Tables
.. _MoinMoin: https://moinmo.in/


Column alignment
~~~~~~~~~~~~~~~~

``tabulate`` is smart about column alignment. It detects columns which
contain only numbers, and aligns them by a decimal point (or flushes
them to the right if they appear to be integers). Text columns are
flushed to the left.

You can override the default alignment with ``numalign`` and
``stralign`` named arguments. Possible column alignments are:
``right``, ``center``, ``left``, ``decimal`` (only for numbers), and
``None`` (to disable alignment).

Aligning by a decimal point works best when you need to compare
numbers at a glance::

    >>> print(tabulate([[1.2345],[123.45],[12.345],[12345],[1234.5]]))
    ----------
        1.2345
      123.45
       12.345
    12345
     1234.5
    ----------

Compare this with a more common right alignment::

    >>> print(tabulate([[1.2345],[123.45],[12.345],[12345],[1234.5]], numalign="right"))
    ------
    1.2345
    123.45
    12.345
     12345
    1234.5
    ------

For ``tabulate``, anything which can be parsed as a number is a
number. Even numbers represented as strings are aligned properly. This
feature comes in handy when reading a mixed table of text and numbers
from a file:

::

    >>> import csv ; from StringIO import StringIO
    >>> table = list(csv.reader(StringIO("spam, 42\neggs, 451\n")))
    >>> table
    [['spam', ' 42'], ['eggs', ' 451']]
    >>> print(tabulate(table))
    ----  ----
    spam    42
    eggs   451
    ----  ----



Custom column alignment
~~~~~~~~~~~~~~~~~~~~~~~

``tabulate`` allows a custom column alignment to override the above.  The
``colalign`` argument can be a list or a tuple of ``stralign`` named arguments.
Possible column alignments are: ``right``, ``center``, ``left``, ``decimal``
(only for numbers), and ``None`` (to disable alignment).  Omitting an alignment
uses the default.  For example::

    >>> print(tabulate([["one", "two"], ["three", "four"]], colalign=("right",))
    -----  ----
      one  two
    three  four
    -----  ----



Number formatting
~~~~~~~~~~~~~~~~~

``tabulate`` allows to define custom number formatting applied to all
columns of decimal numbers. Use ``floatfmt`` named argument::

    >>> print(tabulate([["pi",3.141593],["e",2.718282]], floatfmt=".4f"))
    --  ------
    pi  3.1416
    e   2.7183
    --  ------

``floatfmt`` argument can be a list or a tuple of format strings,
one per column, in which case every column may have different number formatting::

    >>> print(tabulate([[0.12345, 0.12345, 0.12345]], floatfmt=(".1f", ".3f")))
    ---  -----  -------
    0.1  0.123  0.12345
    ---  -----  -------



Text formatting
~~~~~~~~~~~~~~~

By default, ``tabulate`` removes leading and trailing whitespace from text
columns. To disable whitespace removal, set the global module-level flag
``PRESERVE_WHITESPACE``::

    import tabulate
    tabulate.PRESERVE_WHITESPACE = True



Wide (fullwidth CJK) symbols
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To properly align tables which contain wide characters (typically fullwidth
glyphs from Chinese, Japanese or Korean languages), the user should install
``wcwidth`` library. To install it together with ``tabulate``::

    pip install tabulate[widechars]

Wide character support is enabled automatically if ``wcwidth`` library is
already installed.  To disable wide characters support without uninstalling
``wcwidth``, set the global module-level flag ``WIDE_CHARS_MODE``::

    import tabulate
    tabulate.WIDE_CHARS_MODE = False


Multiline cells
~~~~~~~~~~~~~~~

Most table formats support multiline cell text (text containing newline
characters). The newline characters are honored as line break characters.

Multiline cells are supported for data rows and for header rows.

Further automatic line breaks are not inserted. Of course, some output formats
such as latex or html handle automatic formatting of the cell content on their
own, but for those that don't, the newline characters in the input cell text
are the only means to break a line in cell text.

Note that some output formats (e.g. simple, or plain) do not represent row
delimiters, so that the representation of multiline cells in such formats
may be ambiguous to the reader.

The following examples of formatted output use the following table with
a multiline cell, and headers with a multiline cell::

    >>> table = [["eggs",451],["more\nspam",42]]
    >>> headers = ["item\nname", "qty"]

``plain`` tables::

    >>> print(tabulate(table, headers, tablefmt="plain"))
    item      qty
    name
    eggs      451
    more       42
    spam

``simple`` tables::

    >>> print(tabulate(table, headers, tablefmt="simple"))
    item      qty
    name
    ------  -----
    eggs      451
    more       42
    spam

``github`` tables::

    >>> print(tabulate(table, headers, tablefmt="github"))
    | item   | qty   |
    | name   |       |
    |--------|-------|
    | eggs   | 451   |
    | more   | 42    |
    | spam   |       |

``grid`` tables::

    >>> print(tabulate(table, headers, tablefmt="grid"))
    +--------+-------+
    | item   |   qty |
    | name   |       |
    +========+=======+
    | eggs   |   451 |
    +--------+-------+
    | more   |    42 |
    | spam   |       |
    +--------+-------+

``fancy_grid`` tables::

    >>> print(tabulate(table, headers, tablefmt="fancy_grid"))
    ╒════════╤═══════╕
    │ item   │   qty │
    │ name   │       │
    ╞════════╪═══════╡
    │ eggs   │   451 │
    ├────────┼───────┤
    │ more   │    42 │
    │ spam   │       │
    ╘════════╧═══════╛

``pipe`` tables::

    >>> print(tabulate(table, headers, tablefmt="pipe"))
    | item   |   qty |
    | name   |       |
    |:-------|------:|
    | eggs   |   451 |
    | more   |    42 |
    | spam   |       |

``orgtbl`` tables::

    >>> print(tabulate(table, headers, tablefmt="orgtbl"))
    | item   |   qty |
    | name   |       |
    |--------+-------|
    | eggs   |   451 |
    | more   |    42 |
    | spam   |       |

``jira`` tables::

    >>> print(tabulate(table, headers, tablefmt="jira"))
    | item   |   qty |
    | name   |       |
    |:-------|------:|
    | eggs   |   451 |
    | more   |    42 |
    | spam   |       |

``presto`` tables::

    >>> print(tabulate(table, headers, tablefmt="presto"))
     item   |   qty
     name   |
    --------+-------
     eggs   |   451
     more   |    42
     spam   |

``psql`` tables::

    >>> print(tabulate(table, headers, tablefmt="psql"))
    +--------+-------+
    | item   |   qty |
    | name   |       |
    |--------+-------|
    | eggs   |   451 |
    | more   |    42 |
    | spam   |       |
    +--------+-------+

``rst`` tables::

    >>> print(tabulate(table, headers, tablefmt="rst"))
    ======  =====
    item      qty
    name
    ======  =====
    eggs      451
    more       42
    spam
    ======  =====

Multiline cells are not well supported for the other table formats.


Usage of the command line utility
---------------------------------

::

    Usage: tabulate [options] [FILE ...]

    FILE                      a filename of the file with tabular data;
                              if "-" or missing, read data from stdin.

    Options:

    -h, --help                show this message
    -1, --header              use the first row of data as a table header
    -o FILE, --output FILE    print table to FILE (default: stdout)
    -s REGEXP, --sep REGEXP   use a custom column separator (default: whitespace)
    -F FPFMT, --float FPFMT   floating point number format (default: g)
    -f FMT, --format FMT      set output table format; supported formats:
                              plain, simple, github, grid, fancy_grid, pipe,
                              orgtbl, rst, mediawiki, html, latex, latex_raw,
                              latex_booktabs, tsv
                              (default: simple)


Performance considerations
--------------------------

Such features as decimal point alignment and trying to parse everything
as a number imply that ``tabulate``:

* has to "guess" how to print a particular tabular data type
* needs to keep the entire table in-memory
* has to "transpose" the table twice
* does much more work than it may appear

It may not be suitable for serializing really big tables (but who's
going to do that, anyway?) or printing tables in performance sensitive
applications. ``tabulate`` is about two orders of magnitude slower
than simply joining lists of values with a tab, coma or other
separator.

In the same time ``tabulate`` is comparable to other table
pretty-printers. Given a 10x10 table (a list of lists) of mixed text
and numeric data, ``tabulate`` appears to be slower than
``asciitable``, and faster than ``PrettyTable`` and ``texttable``
The following mini-benchmark was run in Python 3.7.1 on Windows

::

    ===========================  ==========  ===========
    Table formatter                time, μs    rel. time
    ===========================  ==========  ===========
    csv to StringIO                    15.6          1.0
    join with tabs and newlines        20.4          1.3
    asciitable (0.8.0)                285.5         18.3
    tabulate (0.8.3)                  685.8         44.0
    PrettyTable (0.7.2)              1277.4         81.9
    texttable (1.6.0)                1744.7        111.8
    ===========================  ==========  ===========

Version history
---------------

The full version history can be found at the `changelog <./CHANGELOG>`_.

How to contribute
-----------------

Contributions should include tests and an explanation for the changes they
propose. Documentation (examples, docstrings, README.rst) should be updated
accordingly.

This project uses `nose`_ testing framework and `tox`_ to automate testing in
different environments. Add tests to one of the files in the ``test/`` folder.

To run tests on all supported Python versions, make sure all Python
interpreters, ``nose`` and ``tox`` are installed, then run ``tox`` in
the root of the project source tree.

On Linux ``tox`` expects to find executables like ``python2.6``,
``python2.7``, ``python3.4`` etc. On Windows it looks for
``C:\Python26\python.exe``, ``C:\Python27\python.exe`` and
``C:\Python34\python.exe`` respectively.

To test only some Python environements, use ``-e`` option. For
example, to test only against Python 2.7 and Python 3.4, run::

    tox -e py27,py34

in the root of the project source tree.

To enable NumPy and Pandas tests, run::

    tox -e py27-extra,py34-extra

(this may take a long time the first time, because NumPy and Pandas
will have to be installed in the new virtual environments)

See ``tox.ini`` file to learn how to use ``nosetests`` directly to
test individual Python versions.

.. _nose: https://nose.readthedocs.org/
.. _tox: https://tox.readthedocs.io/


Contributors
------------

Sergey Astanin, Pau Tallada Crespí, Erwin Marsi, Mik Kocikowski, Bill Ryder,
Zach Dwiel, Frederik Rietdijk, Philipp Bogensberger, Greg (anonymous),
Stefan Tatschner, Emiel van Miltenburg, Brandon Bennett, Amjith Ramanujam,
Jan Schulz, Simon Percivall, Javier Santacruz López-Cepero, Sam Denton,
Alexey Ziyangirov, acaird, Cesar Sanchez, naught101, John Vandenberg,
Zack Dever, Christian Clauss, Benjamin Maier, Andy MacKinlay, Thomas Roten,
Jue Wang, Joe King, Samuel Phan, Nick Satterly, Daniel Robbins, Dmitry B,
Lars Butler, Andreas Maier, Dick Marinus, Sébastien Celles, Yago González,
Andrew Gaul, Wim Glenn, Jean Michel Rouly.

