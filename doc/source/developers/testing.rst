.. _testing:
..  role:: strike

************************
PySAL Testing Procedures
************************
.. contents::

As of PySAL release 1.6, continuous integration testing was ported to the
Travis-CI hosted testing framework (http://travis-ci.org).  There is integration within GitHub that
provides Travis-CI test results included in a pending Pull Request page, so
developers can know before merging a Pull Request that the changes will or will
not induce breakage. 

Take a moment to read about the Pull Request development model on our wiki at
https://github.com/pysal/pysal/wiki/GitHub-Standard-Operating-Procedures

PySAL relies on two different modes of testing [1] integration (regression)
testing and [2] doctests. All developers responsible for given packages shall
utilize both modes.

Integration Testing
===================

Each package shall have a directory `tests` in which unit test scripts for
each module in the package directory are required. 
For example, in the directory `pysal/esda` the module `moran.py` requires a
unittest script named `test_moran.py`. This path for this script needs to be
`pysal/esda/tests/test_moran.py`.

To ensure that any changes made to one package/module do not introduce breakage in the wider project,
developers should run the package wide test suite using nose before making any
commits. As of release version 1.5, all tests must pass using a 64-bit
version of Python.
To run the new test suite, install nose, nose-progressive, and nose-exclude into your
working python installation. If you're using EPD, nose is already available::

    pip install -U nose
    pip install nose-progressive
    pip install nose-exclude

Then::

  cd trunk/
  nosetests pysal/
  
You can also run the test suite from within a Python session. At the
conclusion of the test, Python will, however, exit::
 
  import pysal
  import nose
  nose.runmodule('pysal')


The file setup.cfg (added in revision 1050) in trunk holds nose configuration variables. When nosetests
is run from trunk, nose reads those configuration parameters into its operation,
so developers do not need to specify the optional flags on the command line as
shown below. 

To specify running just a subset of the tests, you can also run::

  nosetests pysal/esda/
  
or any other directory, for instance, to run just those tests. 
To run the entire unittest test suite plus all of the doctests, run::

  nosetests --with-doctest pysal/

To exclude a specific directory or directories, install nose-exclude from PyPi
(pip install nose-exclude). Then run it like this::

  nosetests -v --exclude-dir=pysal/contrib --with-doctest  pysal/


Note that you'll probably run into an IOError complaining about too many open
files. To fix that, pass this via the command line::

  ulimit -S -n 1024

That changes the machine's open file limit for just the current terminal
session. 

The trunk should most always be in a state where all tests are passed.


Generating Unit Tests
=====================

A useful development companion is the package `pythoscope <http://pythoscope.org>`_. It scans
package folders and produces test script stubs for your modules that fail until
you write the tests -- a pesky but useful trait. Using pythoscope in the most
basic way requires just two simple command line calls::
 
 pythoscope --init

 pythoscope <my_module>.py


:strike:`One caveat: pythoscope does not name your test classes in a PySAL-friendly way
so you'll have to rename each test class after the test scripts are generated.`
Nose finds tests!

Docstrings and Doctests
=======================

All public classes and functions should include examples in their docstrings. Those examples serve two purposes:

#. Documentation for users
#. Tests to ensure code behavior is aligned with the documentation

Doctests will be executed when building PySAL documentation with Sphinx.


Developers *should* run tests manually before committing any changes that
may potentially effect usability. Developers can run doctests (docstring
tests) manually from the command line using nosetests ::

  nosetests --with-doctest pysal/

Tutorial Doctests
=================

All of the tutorials are tested along with the overall test suite. Developers
can test their changes against the tutorial docstrings by cd'ing into
/doc/ and running::

    make doctest
