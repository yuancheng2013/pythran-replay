pythran-replay
==============


What is it?
-----------
pythran-replay is a script which replays the commits history of Pythran_


Motivation
----------
Analysing the history commits helps to know how well the code
generated by Pythran performs, and which commits had really helped
improving the performance. Meanwhile, we can also easily find out
if a commit has ever led to a worse performance.


Dependencies
------------

* Pythran_
* gitpython_

 .. _Pythran: https://github.com/serge-sans-paille/pythran
 .. _gitpython: https://github.com/gitpython-developers/GitPython


Usage
-----
**Typical usage**

The easiest way to explain is to give some examples.

1. All by default as it could! As shown by the example below, the program
   will select all the commits in the master branch and try to compile the
   benchmark selected. ::

        $> ./pythran-replay benchmark/dprod.py -s 'import dprod;l0=[1]*100000;l1=[2]*100000' 'dprod.dprod(l0,l1)' --url /path/to/pythran-repo

2. The command below will select the 100 latest commits and compile once every
   10 commits for the benchmark dprod.py
   NB: the 6c16cfd26a0c4cb146c2ffa3b374cbfe6fd2cc97 is the master branch's
   latest commit at the moment (your have to specify the commit id). That means
   it begin with this commit, and select 100 behind it. Then for every 10
   commit it compile once. ::

        $> ./pythran-replay benchmark/dprod.py -s 'import dprod;l0=[1]*100000;l1=[2]*100000' 'dprod.dprod(l0,l1)' --url /path/to/pythran-repo -b master --count_range 6c16cfd26a0c4cb146c2ffa3b374cbfe6fd2cc97:100:10 -n 10 -r 21

   As you saw from the examples above, the interface looks like timeit_ module
   in python except that you have to specify the branch, the pythran path and
   some other options. The -r means the number of repeats for each commit.
   The -n means the number of loops in a repeat.

 .. _timeit: http://docs.python.org/2/library/timeit.html#command-line-interface


3. Select all commits between a start point and an end point.
   Compile for every 2 commits::

        $> ./pythran-replay benchmark/dprod.py -s 'import dprod;l0=[1]*100000;l1=[2]*100000' 'dprod.dprod(l0,l1)' --url /path/to/pythran -b master --range 6c16cfd26a0c4cb146c2ffa3b374cbfe6fd2cc97:8a2df0af49430098c55120bb5cb11f485e9b3eb5:2 -n 10 -r 21


Configuration
-------------

Pythran-replay's configuration file is named ``.replay-config``.
You can find it in the same folder of the scripts. Configuring this file may
simplify your command line and may give more options!

1. To avoid typing ``--url`` and ``--branch`` all the time, you can write it as
   default into your ``.replay-config`` after the ``[user]`` section. ::

        `[user]
        `url = /path/to/pythran
        `branch = <your-branch>

2. Pythran has many other compile options, to add them as compile options
   after the ``[user]`` section. ::

        `[user]
        `cppflags = <pythran-command-line-options>

   For example: ``cppflags = -O2 -fopenmp``.


plot-result
===========
Once the pythran-replay finished, you must be informed that a log file is
built and saved into your current work path. For example, if you have finished
the dprod on your master branch, you should have a log file named
'dprod_master.log'.

1. You can then use the plot-result to produce the curves with matplotlib. ::

        $> ./plot-result dprod_master.log

   More little the commit number is in the x-axis, more recent the commit is.
   For the detail of a certain commit, please checkout your log file.

2. Your benchmark is probably not supported by many of the commits that you have
   selected. To see which of them have passed. ::

        $> ./plot-result dprod_master.log --all

3. Lastly, you can also simply plot the results after executing the pythran-replay
   by adding an option ``--show`` in your pythran-replay's command line.


To be improved
==============
* use distutils to help install dependencies.

* In the plot-result, it is better to gather all information in one loop.
