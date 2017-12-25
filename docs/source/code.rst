atm
======

Adding a classification method
------------------------------

ATM makes it easy to add new methods for classification. 

The method must be implemented by a python class that has the following functions:

1) ``fit``: given training data and labels (X and y), learn a predictive model
2) ``predict``: given unlabeled feature vectors (X), predict the corresponding labels

This follows the convention used by `scikit-learn <http://scikit-learn.org/stable/>`_, and most of the classifier methods already included with ATM are ``sklearn`` classes. However, any custom python class that implements the fit/predict interface can be used with ATM.

1. Creating the JSON file
-------------------------
{   
    "name": "bnb",
    "class": "sklearn.naive_bayes.BernoulliNB",
    "parameters": {...},
    "root_parameters": [...],
    "conditions": {...}
}

        "alpha": {
            "type": "float",
            "range": [0.0, 1.0]
        },

        "kernel": {
            "type": "string",
            "range": ["constant", "rbf", "matern"]
        },

    "root_parameters": ["kernel"],

    "conditions": {
        "kernel": {
            "matern": ["nu"],
            "rational_quadratic": ["length_scale", "alpha"],
            "exp_sine_squared": ["length_scale", "periodicity"]
        }
    }


2. (Optional) Adding a method to the ATM library
------------------------------------------------
If you would like your classifier to be included as one of the 

In ``atm/mapping.py``, enter the function in the ``LEARNER_CODE_CLASS_MAP`` variable and the classifier enumerator (see below) in the ``ENUMERATOR_CODE_CLASS_MAP`` variable.
Decide a code for the classifier (e.g., classify_knn) and enter this in the ``__init__.py`` file in ``atm/enumeration/classification``.

Create an Enumerator for the classifier in the ``atm/enumeration/classification`` folder.
In this file:

a) define the ranges of the parameters in a variable called ``DEFAULT_RANGES``
b) define the parameter types (int, float, etc.) in a variable called ``DEFAULT_KEYS``
c) define the Conditional Parameter Tree (CPT) in a function called ``create_cpt``


Since sklearn uses consistent framework across classifiers (fit, predict, etc.), atm creates a pipeline with the classifier and uses the sklearn standard framework to learn the classifier and make predictions.


Adding a Parameter Selector
---------------------------
A parameter selector can be created by creating a class which inherits the ``SamplesSelector`` class.
The class must have a ``select`` function which returns the chose parameters.
A few examples which use a Gaussian Process to choose parameters is shown below.

.. literalinclude:: ../../atm/selection/samples/gp_ei.py


Changing the Acquisition Function
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Gaussian Process Expected Improvement selection scheme makes use of an acquisition function to decide which parameter set will offer the best performance improvement.
The current acquisition function (seen below) makes use of the predicted performance and the confidence of the performance to create a new metric for deciding which parameter set will likely offer the best performance.
This metric can be altered depending on the needs of a particular problem in the ``acquisition.py`` file in the ``atm/selection`` folder.

.. literalinclude:: ../../atm/selection/acquisition.py



Adding a Frozens (Hyperpartition) Selector
------------------------------------------
A parameter selector can be created by creating a class which inherits the ``FrozenSelector`` class.
The class must have a ``select`` function which returns the chose parameters.
An example which uses the UCB1 algorithm to choose the hyperpartition is shown below.

.. literalinclude:: ../../atm/selection/frozens/ucb1.py
