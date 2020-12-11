.. _rule_policy:

Rules
=====

Rules are a type of training data used to train your assistant's.
Rules describe short pieces of conversations that should always follow the same path.

Rules are great to handle small specific conversation rules don't have the power to generalize to
unseen conversation paths. Combine rules and model based policy to make your assistant robust and
able to handle real user behavior.

Writing a rule
--------------

Before you start writing rules, you have to make sure that the Rule Policy is added to your model configuration:

.. code-block:: yaml

    POLICY:
        policy_0:
          package: comet.agent.core.dialogue_policy.rule_policy
          class: RulePolicy
          arguments:
            training: False
            rules_data_path: applications/babe_sale_bot/data/rules.yaml
            algorithm:
              priority: 4

To indicate that a rule can apply at any point in a conversation, start with the `usr_acts` which starts
the conversation and then add the `system_acts` which your assistant should perform in response to that.

Rules for the Conversation Start
--------------------------------

.. code-block:: yaml

    - rule: Say hello if user greet in first turn of conversation
      conversation_start: True
      steps:
      - usr_acts: greet
      - system_acts: greet

If a user sends a message with the intent greet later in the conversation, the rule will not match.

Rules with Conditions
---------------------

Conditions describe requirements which have to be fulfilled for a rule to be applicable. To do so,
add any information about the prior conversation under the condition key:

Possible information that you can include under condition includes `slot_was_set` events and `attrs` key.


.. code-block:: yaml

    - rule: if user inform and uncertain is set and available slots not empty, -> not_sure;ask_confirm
      condition:
        - attrs:
            - uncertain_is_set: True
            - availability_slots:
                value: null
                operator: not_empty
                interpret:

.. note::

    You may need to see :class:`Dialogue State <comet.agent.core.state.DialogueState>` to know all attributes we can check as well
Condition keys
""""""""""""""

``slot_was_set``: This is tracking all value in ``slots``. ``slots`` is a special dictionary of :class:`Dialogue State <comet.agent.core.state.DialogueState>`. This dictionary will be modified with dialogue state tracking callbacks.

``attrs``: This is any attribute of :class:`Dialogue State <comet.agent.core.state.DialogueState>` for example: `availability_slots`, `availability_slots`, `deny_is_set` ...

Condition operator
""""""""""""""""""

``operator``: The operator will be execute when compare between ``value`` and ``attrs`` value. The default is equal ``eq``. Other operator which is supported:

    - ``not in``: return True if ``value`` not in ``interpret(state value)``
    - ``in``: return True if ``value`` in ``interpret(state value)``
    - ``gt``: return True if ``interpret(state value)`` greater than ``value``
    - ``ge``: return True if ``interpret(state value)`` greater than or equal ``value``
    - ``lt``: return True if ``interpret(state value)`` less than ``value``
    - ``le```: return True if ``interpret(state value)`` less than or equal ``value``
    - ``eq``: return True if ``interpret(state value)`` equal ``value``
    - ``empty``: return True if ``interpret(state value)`` is empty()
    - ``not_empty``: return True if ``interpret(state value)`` is not_empty()
    - ``=``: same as ``eq``
    - ``>=``: same as ``ge``
    - ``>``: same as ``gt``
    - ``<=``: same as ``le``
    - ``<``: same as ``lt``

``interpret``: it's a wrapper function name. ``attrs value`` will be extracted actual value with this function before do ``operator``. The defaults is None. Currently we support: `length`, `keys`, `values`, `int`, `str`, `float`, `abs`

    - ``length``: Get length of value: output = length(value)
    - ``keys``: Get keys of value: output = value.keys()
    - ``values``: Get keys of value: output = value.values()
    - ``int``: Convert to int: output = int(value)
    - ``str``: Convert to str: output = str(value)
    - ``float``: Convert to float: output = float(value)
    - ``abs``: absolute value: output = abs(value)

For example:

.. code-block:: yaml

    - rule: example rule for attr and slots
      condition:
        - attrs:
            - uncertain_is_set: True
            - availability_slots:
                value: null
                operator: not_empty
            - sys_denied_slots:
                value: object_type
                operator: in
                interpret: keys
            - db_result:
                value: 0
                operator: gt
                interpret: length
        - slot_was_set:
            - some_signal: True
            - other_signal: False

The condition is match if current state satisfied:

.. code-block:: python

    is_matched = bool(
        state.uncertain_is_set = True
        and state.availability_slots
        and "object_type" in state.sys_denied_slots.keys()
        and len(state.db_result) > 0
        and state.slots['some_signal'] is True
        and state.slots['other_signal'] is False
    )


Rules with Steps
----------------

To indicate that a rule can apply at any turn in a conversation, start with the `usr_acts` which starts
the conversation and then add the `system_acts`.

- ``usr_acts``: User action, it's support multi intent separate by ``;`` character. For example. "greet;thank"
- ``system_acts``: Expected system action. It's same as with ``usr_acts``.

.. note::

    in ``usr_acts``, and ``system_acts`` we can define both intent and slot. For example:

    ``inform#price;request#color``: the acts will be ``[DialogueAct(inform, price), DialogueAct(request, color)]``

Example:

.. code-block:: yaml

    - rule: example rule for steps
      condition:
        - attrs:
            - uncertain_is_set: True
      steps:
        - usr_acts: inform
        - system_acts: deny
        - usr_acts: agree
        - system_acts: bye

Suppose ``n`` is a last turn of the conversation
The condition is match if history state satisfied bellow condition:

- state[n]: ``uncertain_is_set`` is True
- in state[n]: user intent is: ``agree``
- in state[n-1]: user intent is ``inform`` and system intent is ``agree``

Each step is also support condition rule. For example

.. code-block:: yaml

    - rule: example rule for steps
      condition:
        - attrs:
            - uncertain_is_set: True
      steps:
        - usr_acts: inform
        - attrs:
            - db_result:
                value: 0
                operator: gt
                interpret: length
        - system_acts: deny
        - usr_acts: agree
        - system_acts: bye

The rule is matched if the history state satisfied bellow condition:

- state[n]: ``uncertain_is_set`` is True
- in state[n]: user intent is: ``agree``
- in state[n-1]: user intent is ``inform`` and system intent is ``agree``
- in state[n-1]: ``len(state.db_result)`` > 0

Bellow is full example rule policy applied for babe shop
""""""""""""""""""""""""""""""""""""""""""""""""""""""""

.. code-block:: yaml

    version: "0.3.0"

    rules:
    # RESPONSE GREETING
    - rule: Say greet when user greet
      steps:
        - usr_acts: greet
        - system_acts: greet

    # RESPONSE BYE
    - rule: Say good bye when user is bye
      steps:
        - usr_acts: bye
        - system_acts: bye

    # RESPONSE BYE
    - rule: Say i am bot when system is ask about bot
      steps:
        - usr_acts: ask_is_bot
        - system_acts: i_am_bot
    # RESPONSE THANKS
    - rule: Thanks and say goodbye when user thanks
      steps:
        - usr_acts: thanks
        - system_acts: thanks;bye

    # RESPONSE INFORM
    - rule: if user inform and uncertaint is set and available slots not empty, -> not_sure;ask_confirm
      condition:
        - attrs:
            - uncertain_is_set: True
            - availability_slots:
                value: null
                operator: not_empty
                interpret:
      steps:
        - usr_acts: inform
        - system_acts: not_sure;ask_confirm

    - rule: if user inform and uncertaint is set and available slots empty, -> not_sure
      condition:
        - attrs:
            - uncertain_is_set: True
            - availability_slots:
                value: null
                operator: empty
      steps:
        - usr_acts: inform
        - system_acts: not_sure

    - rule: if user inform and deny is set and available slots not empty, -> deny;ask_confirm
      condition:
        - attrs:
            - sys_deny_is_set: True
            - sys_availability_slots:
                value: null
                operator: not_empty
                interpret:
      steps:
        - usr_acts: inform
        - system_acts: deny;ask_confirm

    - rule: if user inform with uncertain_is_set is False, and 0 < db < 3 -> make_offer
      condition:
        - attrs:
            - uncertain_is_set: False
            - db_result:
                value: 3
                operator: "le"
                interpret: "length"
            - db_result:
                value: 0
                operator: "gt"
                interpret: "length"
      steps:
        - usr_acts: inform
        - system_acts: offer

    - rule: if user inform with uncertain_is_set is False, and db > 3 -> ask_confirm
      condition:
        - attrs:
            - uncertain_is_set: False
            - db_result:
                value: 3
                operator: "gt"
                interpret: "length"
      steps:
        - usr_acts: inform
        - system_acts: ask_confirm

    # RESPONSE AGREE
    - rule: If user agree and the product available -> make offer
      condition:
        - attrs:
            - db_result:
                value: 3
                operator: "le"
                interpret: "length"
            - db_result:
                value: 0
                operator: gt
                interpret: "length"
      steps:
        - usr_acts: agree
        - system_acts: offer

    - rule: If user agree and the product available, And db_result > 3 -> make ask_confirm
      condition:
        - attrs:
            - db_result:
                value: 3
                operator: gt
                interpret: "length"
      steps:
        - usr_acts: agree
        - system_acts: ask_confirm

    - rule: If user agree and both database and availability_slots are empty() -> deny
      condition:
        - attrs:
            - db_result:
                value: null
                operator: empty
            - availability_slots:
                value: null
                operator: empty
      steps:
        - usr_acts: agree
        - system_acts: deny

    - rule: If user agree, db=0 and availability_slots is not empty -> deny;ask_confirm
      condition:
        - attrs:
            - db_result:
                value: null
                operator: empty
            - availability_slots:
                value: null
                operator: not_empty
      steps:
        - usr_acts: agree
        - system_acts: deny;ask_confirm
    # RESPONSE DENY
    - rule: If user deny, db>0 and availability_slots is not empty -> deny;ask_confirm
      condition:
      steps:
        - usr_acts: deny
        - system_acts: request
    # RESPONSE REQUEST
    - rule: If user request and found product available -> inform
      condition:
        - attrs:
            - db_result:
                value: 0
                operator: gt
                interpret: "length"
      steps:
        - usr_acts: request
        - system_acts: inform
    # RESPONSE ASK_CONFIRM
    - rule: If user ask_confirm -> confirm_answer
      condition:
        - attrs:
            - db_result:
                value: 0
                operator: gt
                interpret: "length"
      steps:
        - usr_acts: ask_confirm
        - system_acts: confirm_answer
    # RESPONSE INFORM;REQUEST
    - rule: If user inform;request and found product available -> inform
      condition:
        - attrs:
            - db_result:
                value: 0
                operator: gt
                interpret: "length"
      steps:
        - usr_acts: inform;request
        - system_acts: inform
