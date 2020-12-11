Supervised policy training data
-------------------------------

The data training format for policy we use as ``record format`` which is a ``object``. We can show as:

.. code-block:: json

    [{
    "state": "the state from which action was taken",
    "new_state": "the resulting state after action was taken",
    "action": [
        "the action taken from state"
    ],
    "reward": "the reward received after taking action from state",
    "usr_transcripts": "",
    "system_transcripts": "Hello , How may I help you?",
    "success": "",
    "task_success": "",
    "cumulative_reward": -1,
    "role": "system",
    "custom": {}
    }]

Above is an object's format. We can feed the ``raw conversation`` or ``record format``. Based on
what you feed to, the system will convert ``training data`` to ``record format``  to train the policy.

There are two way to prepare data training: ``manual`` or using ``user simulator``

Manual preparing
""""""""""""""""

To manual preparing data format for policy training. We have to label the conversation following
format as a ``.json`` file:

.. code-block:: json

    [
    {
        "dialogue_idx": 0,
        "turns": [
            {
                "turn_idx": -1,
                "usr_semantics": [
                    {
                        "slots": {"slot2": "value1"},
                        "act": "inform"
                    }
                ],
                "usr_transcript": "",
                "system_transcript": "",
                "system_semantics": [],
                "db_result": {}
            },
            {}
        ],
    },
    {}
    ]

Generate by interact between agent and user simulator
"""""""""""""""""""""""""""""""""""""""""""""""""""""

To use this way, we need to have a ``User simulator`` and Agent's ``Handcraft policy``.

For more make sense in the way to generate data by simulation. We should play around with ``BabeShop``
domain that we are implemented.

See this section to know how to run ``BabeShop``'s domain and then run commandline bellow to run simulation

.. code-block:: json
    cais run --config config/configure_train.yaml

In folder ``assets/BabeShop/v0.2.0/records`` you will something like this.


.. code-block::

    assets
    └── BabeShop
        └── v0.2.0
            └── records
                ├── train_supervised_dialogues.json
                └── train_supervised_dialogues.pkl