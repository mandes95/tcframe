API reference
=============

.. _api-ref-problem-configuration:

Problem configuration
---------------------

The following methods can be called inside the overridden method **BaseProblem::Config()**.

.. cpp:function:: void setSlug(string slug)

    Sets the *slug* of the problem. A slug is a nickname or code for the problem. The produced test case filenames will have the slug as prefix. For example, if the slug is helloworld" then one valid test case filename is "helloworld_1.in".

    If not specified, the default slug is "problem".

.. _api-ref-io-variables:

Input/output variables
----------------------

There are three types of variables that are supported:

Scalar
    Variables of built-in integral types (int, long long, char, etc.), built-in floating-point types (float, double), and std::string.

Vector
    std::vector<\ **T**\ >, where **T** is a scalar type as defined above. Note that you cannot use arrays (\ **T**\ []).

Matrix
    std::vector<std::vector<\ **T**\ >>, where T is a scalar type as defined above. Note that you cannot use 2D arrays (\ **T**\ [][]).

.. _api-ref-io-segments:

Input/output segments
---------------------

The following macros can be called inside the overridden method **BaseProblem::InputFormat()** or **BaseProblem::OutputFormat()**.

.. py:function:: EMPTY_LINE()

    Defines an empty line.

.. py:function:: LINE(comma-separated elements)

    Defines a single line containing space-separated scalar or vector variables. In case of vector variables, the elements are separated by spaces as well.

    **element** is one of:

    - *<scalar variable name>*.
    - *<vector variable name>* **% SIZE(**\ *<number of elements>*\ **)**. The number of elements can be a constant or a scalar variable.

    For example:

    .. sourcecode:: cpp

        void InputFormat() {
            LINE(N);
            LINE(A % SIZE(3));
            LINE(M, B % SIZE(M));
        }

    With **N** = 2, **A** = {1, 2, 3}, **M** = 2, **B** = {7, 8}, the above segments will produce:

    ::

        2
        1 2 3
        2 7 8

.. py:function:: LINES(comma-separated vector variable names) % SIZE(number of elements)

    Defines multiple lines, each consisting space-separated elements of given vector variables.

    For example:

    .. sourcecode:: cpp

        void InputFormat() {
            LINES(V) % SIZE(2);
            LINES(X, Y) % SIZE(N);
        }

    With **V** = {1, 2}, **X** = {100, 110, 120}, **Y** = {200, 210, 220}, **N** = 3, the above segments will produce:

    ::

        1
        2
        100 200
        110 210
        120 220

.. py:function:: GRID(matrix variable name) % SIZE(number of rows, number of columns)

    Defines a grid consisting elements of a given matrix variable. If the given matrix variable is of type char, the elements in each row is not space-separated, otherwise they are space-separated.

    For example:

    .. sourcecode:: cpp

        void InputFormat() {
            GRID(G) % SIZE(2, 2);
            GRID(H) % SIZE(R, C);
        }

    With **G** = {{'a', 'b'}, {'c', 'd'}}, **H** = {{1, 2, 3}, {4, 5, 6}}, **R** = 2, **C** = 3, the above segments will produce:

    ::

        ab
        cd
        1 2 3
        4 5 6

.. _api-ref-constraints:

Constraints
-----------

The following macros can be called inside the overridden method **BaseProblem::Constraints()** or **BaseProblem::SubtaskX()**.

.. py:function:: CONS(predicate)

    Defines a constraint. **predicate** is a boolean expression, whose value must be completely defined by the values of the input variables (only).

    For example:

    .. sourcecode:: cpp

        void Subtask1() {
            CONS(A <= B && B <= 1000);
            CONS(graphDoesNotHaveCycles());
        }

.. _api-ref-generator-configuration:

Generator configuration
-----------------------

The following methods can be called inside the overridden method **BaseGenerator::Config()**.

.. cpp:function:: void setBaseDir(string directoryName)

  Sets the directory for the generated test case files, relative to the location of the generator program.

  If not specified, the default directory name is "tc".

.. cpp:function:: void setSolution(string solutionExecutionCommand)

  Sets the command for executing the official solution. This will be used for generating test case output files. For
  each input files, this will be executed:

  .. sourcecode:: bash

      solutionExecutionCommand < [input filename] > [output filename]

  If not specified, the default command is "./solution".

.. _api-ref-test cases:

Test cases
----------

The following macros can be called inside the overridden method **BaseGenerator::())**.

.. cpp:function:: void assignToSubtasks(set<int> subtaskNumbers)

    Assigns the current test test group to a set of subtasks.

    For example:

    .. sourcecode:: cpp

        void TestGroup1() {
            assignToSubtasks({1, 3});

            // test case definitions follow
        }

The following macros can be called inside the overridden method **BaseGenerator::TestCases()** or **BaseGenerator::TestGroupX()**.

.. py:function:: CASE(comma-separated statements)

    Defines a test case.

    **statement** should be one of:

    - assignment to an input variables
    - private method call that assigns values to one or more input variables

    For example:

    .. sourcecode:: cpp

        void TestCases() {
            CASE(N = 42, M = 100, randomArray());
            CASE(N = 1000, M = 1000, randomArray());
        }

The following macros can be called inside the overridden method **BaseGenerator::SampleTestCases()**.

.. py:function:: SAMPLE_CASE(list of lines, [list of subtask numbers])

    Defines a sample test case. A sample test case is defined as an exact literal string, given as list of lines. **list of subtask numbers** are only valid in problems with subtasks.

    For example, to define this sample test case:

    ::

        1 2
        3 4 5

    You can do this way:

    .. sourcecode:: cpp

        void SampleTestCases() {
            SAMPLE_CASE({
                "1 2",
                "3 4 5"
            });
        }

    for problems without subtasks. For problems with subtasks:

    .. sourcecode:: cpp

        void SampleTestCases() {
            SAMPLE_CASE({
                "1 2",
                "3 4 5"
            }, {1, 3});
        }

    assuming that the sample test case is assigned to subtasks 1 and 3.

    Multiple sample test cases can be defined inside the same method.
