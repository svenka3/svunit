Writing Unit Tests
==================

The unit test template includes embedded instructions on structure and position of unit tests:

.. image:: ../user_guide_files/Screen-Shot-2015-07-03-at-4.41.54-PM.png
    :width: 276

Multiple unit tests can be defined in a unit test template. All must be defined between the \`SVUNIT_TESTS_BEGIN and \`SVUNIT_TESTS_END macros. Individual unit tests are defined using the \`SVTEST(<name>) and \`SVTEST_END macros. The test <name> must be a valid verilog code block label (i.e. any alphanumeric starting with [_a-zA-Z]). For example, a template with 2 tests called test0 and test1 would be declared as:

.. image:: ../user_guide_files/Screen-Shot-2015-07-03-at-4.44.52-PM.png
    :width: 170

The macros expand to a Verilog code block so any code that is legal within a code block can be used within a unit test. Other required variables, declarations, functions, tasks, etc must be defined outside the BEGIN/END macros. For example, the function helper() can be defined and used within a unit test as:

.. image:: ../user_guide_files/Screen-Shot-2015-07-03-at-4.50.11-PM.png
    :width: 162

SVUnit imposes no limit on the number of unit tests that can be defined with a unit test template.


SVUnit Reporting Macros
-----------------------

SVUnit includes an integrated reporting mechanism such that the exit PASS/FAIL status of every unit test is collected, reported and used to report a cumulative result. To set PASS/FAIL status, SVUnit defines several logging macros that are integrated with the reporting structure.

.. image:: ../user_guide_files/Screen-Shot-2015-07-03-at-4.59.01-PM.png
    :width: 245

The most commonly used macros are \`FAIL_IF and \`FAIL_UNLESS that take a single boolean expression as input. The \`FAIL_IF_EQUAL and \`FAIL_UNLESS_EQUAL macros exit based on an '===' comparison of boolean inputs a and b. Likewise, \`FAIL_IF_STR_EQUAL and \`FAIL_UNLESS_STR_EQUAL do a string comparison between inputs a and b.


Setting Test Exit Status
------------------------

The reporting macros can be used to verify outputs and response of a UUT and set test exit status according. For example, we can use the \`FAIL_IF and \`FAIL_UNLESS_EQUAL macros to verify oBus and oPin are driven to the proper state.

.. image:: ../user_guide_files/Screen-Shot-2015-07-08-at-10.37.08-AM.png
    :width: 225

If the conditions described by the macros in either test0 or test1 are not satisfied, the test fails with an assert error and is reported as having failed. Tests are killed immediately at first failure so any code appearing after the failing assert statement does not execute.


Interacting with the UUT
------------------------

Tests can interact with the UUT using simple procedural assignments to inputs or through helper functions and tasks for more complex interactions. For example, if the state of the oPin and oBus outputs is conditional based on the state of the iPin, oPin and oBus can be verified by driving iPin as necessary:

.. image:: ../user_guide_files/Screen-Shot-2015-07-08-at-10.46.19-AM.png
    :width: 251

.. note::

    The addition of the #0 assumes oPin and oBus are asynchronous outputs that require a delta cycle be consumed before they reach their intended state.


Test Setup and Teardown
-----------------------

For behaviour that is repeated before and after every test, the setup() and teardown() tasks in the unit test template are intended to group any logic that is repeated before and/or after every test - the setup() task is run before every test and the teardown() task is run after every test. For example, if the default state of iPin is logic 1, that assignment can be done in the setup task rather than each individual test:

.. image:: ../user_guide_files/Screen-Shot-2015-07-08-at-10.52.09-AM.png
    :width: 268

As a result of moving the 'iPin = 1' assignment to the setup() task, test0 and test1 can be simplified to:

.. image:: ../user_guide_files/Screen-Shot-2015-07-08-at-10.53.10-AM.png
    :width: 245

It is recommended that common initialization code be contained in the setup() task. Reset sequence or register initialization, for example, is common logic that should be included in the setup() task. As well, it is recommended that any general cleanup of the UUT or unit test harness be grouped in the teardown() task to avoid polluting the state space for subsequent tests (i.e teardown() is for "cleaning the slate").
