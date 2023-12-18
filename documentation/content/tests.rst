===============
  About Tests
===============

.. _tests:

Kw's codebase is composed of many components. Each component has a source file
and should have a corresponding test file. These test files are located in
``tests/`` and are named against their modules. Thus, the following naming
convention applies::

  test/component_test.sh

Where `component` is the component's source file name. 

Writing Tests
-------------

Having a good test base is very important in software development. 
This is especially true in open-source projects, where new contributors
may come and go over time, leaving old `bugs` as well as introducing new ones.

It is recommended to create tests whenever possible, but there are some
occasions where writing tests are mandatory for the project. These are:

- When contributing new components
- When refactoring large portions of code
- When a bug is found

For the first two cases, it is important to write both `white box` and
`black box` tests, that is, tests that rely on known implementation
details, such as testing specific portions of the code, and tests 
that only relies on verifying the corrected output for a given
input, respectively.

Running Tests
-------------

Kw's tests rely on `shunit2`. The ``run_tests.sh`` script automatically detects
``shunit2`` in ``$PATH`` in case it is installed in your system. Alternatively, you
can have shunit2 source code in ``tests/`` (you can clone it from
https://github.com/kward/shunit2).

If you want to run all the tests, try::

  ./run_tests.sh

List all available test files::

  ./run_tests.sh list

Or run individual tests with::

  ./run_tests.sh test TESTFILE1 ...

Kw is already prepared to run tests, build the documentation and check the
installation in the github workflow.

`TEST_MODE` Flag
----------------

In Kw's tests, you will often encounter the ``TEST_MODE`` flag being passed as an
argument to the tested function, such as ``kworkflow_man feature TEST_MODE``.
As expected, this alters the function`s behavior; but how exactly?

There are various ways in which ``TEST_MODE`` can modify its behavior. However,
it is common for it to specifically impact the call to the `cmd_manager`
function.

The ``cmd_manager`` function is extensively used in the KW codebase, serving as a
key component for evaluating bash commands. It also has the flexibility to
receive ``TEST_MODE`` as an argument, which is normaly present when the caller
is previously executing with the ``TEST_MODE`` flag.
When this flag is passed to ``cmd_manager``, it alters the behavior of the
function so that the requested command is prevented from evaluation, and
instead, it is solely printed to Stdout.

There are some beneficial consequences from this testing behavior. For
instance, it can be useful for testing if the command that would be executed is
the expected one. Also, it helps tests contexts in general, since we may not
always be interested in connection or environment errors. For example, a ``curl``
might fail if connection is off, or some command (such as ``kw deploy``) might
require an existant possible remote connection (which could be to an already
running virtual machine).

Due to the "non-execution" of the command given to ``cmd_manager`` during
testing, if required, it is expected that the test itself uses mocks to
substitute the command's execution. For example, if the command not executed
due to ``TEST_MODE`` flag is a ``curl``, the test would likely depend on
preprocessed files with contents similar to what would be downloaded in a normal
usage.

