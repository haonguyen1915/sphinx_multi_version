
POLICIES
--------
The assistant uses policies to decide which action to take at each step in a conversation.
There are machine-learning and rule-based policies that your assistant can use in tandem.

You can customize the policies your assistant uses by specifying the policies key in your
project's config.yml. There are different policies to choose from, and you can include multiple
policies in a single configuration. Here's an example of what a list of policies might look like

.. code-block:: yaml

    POLICY:
        policy_0:
          package: src.babe_warm_up_policy
          class: BabeWarmUpPolicy
          arguments:
            algorithm:
                priority: 3
        policy_1:
          package: comet.agent.core.dialogue_policy.supervised_learning.svm_classifier_policy
          class: SVMPolicy
          arguments:
            WP: auto
            train: 0
            algorithm:
                priority: 2
        policy_2:
          package: comet.agent.core.dialogue_policy.supervised_learning.vanilla_classifier_policy
          class: VanillaPolicy
          arguments:
            WP: auto
            train: 0
            algorithm:
                priority: 1


Policy Priority
"""""""""""""""
In the case that two policies predict with equal confidence or greater than a policy threshold, the
priority of the policies is considered. Comet have default priorities that are set to ensure the
expected outcome in the case of a tie. They look like this, where higher numbers have higher priority:

6 - BabeHandcraftPolicy

3 - SVMPolicy

1 - VanillaPolicy

Policy algorithms
"""""""""""""""""

BabeHandcraftPolicy
"""""""""""""""""""

**Configuration**

.. code-block:: yaml

    POLICY:
        policy_0:
          package: src.babe_warm_up_policy
          class: BabeWarmUpPolicy
          arguments:
            algorithm:
                priority: 3

**Arguments**

- ``priority``: the priority of this policy

SVMPolicy
"""""""""

**Configuration**

.. code-block:: yaml

    POLICY:
        policy_0:
            package: comet.agent.core.dialogue_policy.supervised_learning.svm_classifier_policy
            class: SVMPolicy
            arguments:
                algorithm:
                    priority: 3
**Arguments**

- ``SF``: State feature module
- ``training``: If ``True`` it will train the policy with train data provided in ``GENERAL`` section
- ``testing``: If ``True`` it will test the policy model with test data provided in ``GENERAL`` section
- ``algorithm``: a dictionary of

    - ``priority``: the priority of this policy
VanillaPolicy
"""""""""""""

**Configuration**

.. code-block:: yaml

    POLICY:
        policy_0:
            package: comet.agent.core.dialogue_policy.supervised_learning.vanilla_classifier_policy
            class: VanillaPolicy
            arguments:
                algorithm:
                    priority: 3
**Arguments**

- ``SF``: State feature module
- ``training``: If ``True`` it will train the policy with train data provided in ``GENERAL`` section
- ``testing``: If ``True`` it will test the policy model with test data provided in ``GENERAL`` section

- ``algorithm``: a dictionary of

    - ``priority``: the priority of this policy
    - ``training_epoch``: Number of epoch to train
    - ``learning_rate``: The learning rate
    - ``batch_size``: Batch size
    - ``momentum``: The momemtum
    - ``confidence_threshold``: The confidence thresh, use to select best option form multi policies
LSTMPolicy
""""""""""

**Configuration**

.. code-block:: yaml

    POLICY:
        policy_0:
            package: comet.agent.core.dialogue_policy.supervised_learning.lstm_classifier_policy
            class: LSTMPolicy
            arguments:
                algorithm:
                    training_epoch: 100
**Arguments**

- ``SF``: State feature module
- ``training``: If ``True`` it will train the policy with train data provided in ``GENERAL`` section
- ``testing``: If ``True`` it will test the policy model with test data provided in ``GENERAL`` section

- ``algorithm``: a dictionary of

    - ``priority``: the priority of this policy
    - ``training_epoch``: Number of epoch to train
    - ``learning_rate``: The learning rate
    - ``batch_size``: Batch size
    - ``momentum``: The momemtum
    - ``hidden_dim``: number of hidden layer
    - ``num_states``: number of history states use for featurize
    - ``confidence_threshold``: The confidence thresh, use to select best option form multi policies