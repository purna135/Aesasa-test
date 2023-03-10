
.. _nan_tutorial:

=================
Dealing with NaNs
=================

Having a model yielding NaNs or Infs is quite common if some of the tiny
components in your model are not set properly. NaNs are hard to deal with
because sometimes it is caused by a bug or error in the code, sometimes it's
because of the numerical stability of your computational environment (library
versions, etc.), and even, sometimes it relates to your algorithm. Here we try
to outline common issues which cause the model to yield NaNs, as well as
provide nails and hammers to diagnose it.


Check Superparameters and Weight Initialization
-----------------------------------------------

Most frequently, the cause would be that some of the hyperparameters, especially
learning rates, are set incorrectly. A high learning rate can blow up your whole
model into NaN outputs even within one epoch of training. So the first and
easiest solution is try to lower it. Keep halving your learning rate until you
start to get reasonable output values.

Other hyperparameters may also play a role. For example, are your training
algorithms involve regularization terms? If so, are their corresponding
penalties set reasonably? Search a wider hyperparameter space with a few (one or
two) training epochs each to see if the NaNs could disappear.

Some models can be very sensitive to the initialization of weight vectors. If
those weights are not initialized in a proper range, then it is not surprising
that the model ends up with yielding NaNs.


Run in NanGuardMode, DebugMode, or MonitorMode
-----------------------------------------------

If adjusting hyperparameters doesn't work for you, you can still get help from
Aesara's NanGuardMode. Change the mode of your aesara function to NanGuardMode
and run them again. The NanGuardMode will monitor all input/output variables in
each node, and raises an error if NaNs are detected. For how to use the
``NanGuardMode``, please refer to :ref:`nanguardmode`. Using ``optimizer_including=alloc_empty_to_zeros``
with ``NanGuardMode`` could be helpful to detect NaN, for more information please refer
to :ref:`AllocEmpty`.

DebugMode can also help. Run your code in DebugMode with flag
``mode=DebugMode,DebugMode__check_py=False``. This will give you clue about which
op is causing this problem, and then you can inspect that op in more detail. For
details of using ``DebugMode``, please refer to :ref:`debugmode`.

Aesara's MonitorMode provides another helping hand. It can be used to step
through the execution of a function. You can inspect the inputs and outputs of
each node being executed when the function is called. For how to use that,
please check :ref:`faq_monitormode`.


Numerical Stability
-------------------

After you have located the op which causes the problem, it may turn out that the
NaNs yielded by that op are related to numerical issues. For example,
:math:`1 / log(p(x) + 1)` may result in NaNs for those nodes who have learned to
yield a low probability p(x) for some input x.


Algorithm Related
-----------------

In the most difficult situations, you may go through the above steps and find
nothing wrong. If the above methods fail to uncover the cause, there is a good
chance that something is wrong with your algorithm. Go back to the mathematics
and find out if everything is derived correctly.


CUDA Specific Option
--------------------

The Aesara flag ``nvcc.fastmath=True`` can generate NaN. Don't set
this flag while debugging NaN.


.. _AllocEmpty:

NaN Introduced by AllocEmpty
-----------------------------------------------

AllocEmpty is used by many operation such as scan to allocate some memory
without properly clearing it. The reason for that is that the allocated memory
will subsequently be overwritten. However, this can sometimes introduce NaN
depending on the operation and what was previously stored in the memory it is
working on. For instance, trying to zero out memory using a multiplication
before applying an operation could cause NaN if NaN is already present in the
memory, since `0 * NaN => NaN`.

Using ``optimizer_including=alloc_empty_to_zeros`` replaces `AllocEmpty` by
`Alloc{0}`, which is helpful to diagnose where NaNs come from. Please note that
when running in `NanGuardMode`, this rewrite is not included by
default. Therefore, it might be helpful to use them both together.
