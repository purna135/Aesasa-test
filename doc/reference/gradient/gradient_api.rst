.. _reference_gradient_api:

=============
Gradients API
=============


Symbolic gradient is usually computed from :func:`gradient.grad`, which offers a
more convenient syntax for the common case of wanting the gradient of some
scalar cost with respect to some input expressions. The :func:`grad_sources_inputs`
function does the underlying work, and is more flexible, but is also more
awkward to use when :func:`gradient.grad` can do the job.


Gradient related functions
==========================

.. automodule:: aesara.gradient
    :members:

.. _R_op_list:


List of Implemented R op
========================


See the :ref:`gradient tutorial <reference_gradient_tutorial>` for the R op documentation.

list of ops that support R-op:
 * with test
    * SpecifyShape
    * MaxAndArgmax
    * Subtensor
    * IncSubtensor set_subtensor too
    * Alloc
    * Dot
    * Elemwise
    * Sum
    * Softmax
    * Shape
    * Join
    * Rebroadcast
    * Reshape
    * DimShuffle
    * Scan [In tests/scan/test_basic.test_rop]

 * without test
    * Split
    * ARange
    * ScalarFromTensor
    * AdvancedSubtensor1
    * AdvancedIncSubtensor1
    * AdvancedIncSubtensor

Partial list of ops without support for R-op:

 * All sparse ops
 * All linear algebra ops.
 * PermuteRowElements
 * AdvancedSubtensor
 * TensorDot
 * Outer
 * Prod
 * MulwithoutZeros
 * ProdWithoutZeros
 * CAReduce(for max,... done for MaxAndArgmax op)
 * MaxAndArgmax(only for matrix on axis 0 or 1)
