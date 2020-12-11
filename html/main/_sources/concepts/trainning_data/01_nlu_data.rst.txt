.. _nlu_training_data:

NLU training data
-----------------

NLU training data stores structured information about user messages.

The goal of NLU (Natural Language Understanding) is to extract structured information from user messages.
This usually includes the user's intent and any entities their message contains. You can add extra
information such as regular expressions and lookup tables to your training data to help the model
identify intents and entities correctly.

NLU data's training format we construct based on `Denver core <https://nlp.pages.gitlab.ftech.ai/research/denver_core/index.html>`_
Below is it's format as ``.csv`` file

.. code-block:: csv

    text,intent,tag
    hi,greet,O
    hello,greet,O
    bye,bye,O
    bye bye,bye,O O
    are you bot?,ask_is_bot,O O O
    are u bot?,ask_is_bot,O O O
    are u bot,ask_is_bot,O O O
    thanks,thanks,O
    thank,thanks,O
    thank you,thanks,O
    thank you so much,thanks,O
    what is the weather in day 1/1,request#weather,O O O O O O B-inform#date

.. _supervised_policy_training_data: