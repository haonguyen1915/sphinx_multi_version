Step 9: Prepare Training Data
==============================

In this step, i guide you the way to prepare training data for NLU and Policy. About training format,
we have a guide in further tutorial. See :ref:`training_data_format` for detail.

NLU training data
-----------------

In section :ref:`NLU training data <nlu_training_data>` we already defined the data format. All we need to now is
label as .csv file. Bellow is a sample for ``WeatherAsking` domain

train.csv

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
    what is the weather in day 1/2,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/3,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/4,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/5,request#weather,O O O O O O B-inform#date
    what is the temp?,request#temp,O O O O
    what is the temperature?,request#temp,O O O O
    what is the temperature,request#temp,O O O O
    what is the temperture?,request#temp,O O O O
    what is the temperture,request#temp,O O O O
    what is the temperature,request#temp,O O O O
    what is the humidity,request#humidity,O O O O
    what is the humidty,request#humidity,O O O O
    what is the humdity,request#humidity,O O O O
    day 1/1,inform,O B-inform#date
    day 1/2,inform,O B-inform#date
    day 1/3,inform,O B-inform#date
    day 1/4,inform,O B-inform#date
    day 1/5,inform,O B-inform#date
    temp 30,inform,O B-inform#temp
    temp 31,inform,O B-inform#temp
    temp 32,inform,O B-inform#temp
    temp 33,inform,O B-inform#temp
    temp 34,inform,O B-inform#temp

test.csv

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
    what is the weather in day 1/2,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/3,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/4,request#weather,O O O O O O B-inform#date
    what is the weather in day 1/5,request#weather,O O O O O O B-inform#date
    what is the temp?,request#temp,O O O O
    what is the temperature?,request#temp,O O O O
    what is the temperature,request#temp,O O O O
    what is the temperture?,request#temp,O O O O
    what is the temperture,request#temp,O O O O
    what is the temperature,request#temp,O O O O
    what is the humidity,request#humidity,O O O O
    what is the humidty,request#humidity,O O O O
    what is the humdity,request#humidity,O O O O
    day 1/1,inform,O B-inform#date
    day 1/2,inform,O B-inform#date
    day 1/3,inform,O B-inform#date
    day 1/4,inform,O B-inform#date
    day 1/5,inform,O B-inform#date
    temp 30,inform,O B-inform#temp
    temp 31,inform,O B-inform#temp
    temp 32,inform,O B-inform#temp
    temp 33,inform,O B-inform#temp
    temp 34,inform,O B-inform#temp

Policy data training
--------------------

.. note::

    About conversation data format, see :ref:`Supervised policy training data <supervised_policy_training_data>`. There're two way to prepare data: ``manual`` or use ``User Simulator``. In this guide, we will generate by ``User Simulator``.

All you have to do is modified configuration and run.

.. note::
    We've explained for detail the way to configure in: :ref:`configuration <configuration>` and :ref:`connect the components <connect_all_components>`. You should walk through these sections first.

To create a configure to generate conversation data, We have to configure:

1. Set simulation mode.

.. code-block:: yaml

    GENERAL:
      # Can be: simulation, text
      interaction_mode: simulation

2. Enable save mode and set up path to save dialogue in ``GENERAL``

.. code-block::

    GENERAL:
        ...
        # Handle internal experience logs. These are logs produced by the
        # dialogue Recorder (under utilities) and contain information about
        # what has happened during the dialogue(s). agents use these logs as
        # data for training.
        experience_logs:
            save: True
            load: False
            path: assets/weather_asking/v0.2.0/records/train_supervised_dialogues.pkl
            test_path: null
            eval_path: null

.. note::

    We will generate ``test data`` and ``eval data`` in the same way.

3. Set number of dialogue you want to generate in ``DIALOGUE`` field. In this case we set ``num_dialogues`` =100

.. code-block:: yaml

    DIALOGUE:
      # How many dialogues to run for
      num_dialogues: 100

      # Which role has the initiative (speaks first): system or user
      initiative: system

4. Configure the rest of components as same as :ref:`This section <connect_all_components>`

5. Now, just run pipeline with the configure we've done by commandline interface.

.. code-block:: shell

    comet run --config your_config_path.yaml

After that you will see ``train_supervised_dialogues.pkl`` in ``assets/weather_asking/v0.2.0/records/` directory:

.. code-block::

    ./assets/WeatherAsking/v0.2.0/records
    ├── train_supervised_dialogues.json
    └── train_supervised_dialogues.pkl

.. note::
    - ``train_supervised_dialogues.pkl``: This is an instance object of conversation data generated by system, it's directly use to train supervised policy.
    - ``train_supervised_dialogues.json``: This file is converted from ``train_supervised_dialogues.pkl`` use to visualize.


Bellow is full configure to generate conversation data

.. code-block:: yaml

    GENERAL:
      # Can be: simulation, text
      interaction_mode: simulation
      models_path: ./models/WeatherAsking

      # How many times to run this experiment. Each experiment will run the
      # number of dialogues specified in the DIALOGUE section. This is useful
      # for example when evaluating stochastic dialogue policies, where we want
      # to calculate the average performance over several runs.
      runs: 1

      # Handle internal experience logs. These are logs produced by the
      # dialogue Recorder (under utilities) and contain information about
      # what has happened during the dialogue(s). agents use these logs as
      # data for training.
      experience_logs:
        save: True
        load: False
        path: assets/weather_asking/v0.2.0/records/train_supervised_dialogues.json
        test_path: null
        eval_path: null

      # These are data arguments and are added
      # to the arguments of each module of each agent. In this template example we
      # provide the ontology, database, and domain that every module needs.
      data:
        # The path to the ontology .json file
        ontology: data/weather_ontology.json

        # The path to the database .db file. json, sql, neo4j
        database:
          package: src.weather_database
          class: JSONDataBase
          arguments:
            db_path: data/weather_database.json

        #The current domain
        domain: data/weather_domain.json

    DIALOGUE:
      # How many dialogues to run for
      num_dialogues: 10

      # Which role has the initiative (speaks first): system or user
      initiative: system

    AGENT:
      # The role of the agent: system or user
      role: system

      # The config module for dialogue management
      DM:
        package: comet.agent.core.dialogue_manager.dialogue_manager
        class: DialogueManager

      # The config module for dialogue state
      DS:
        package: comet.agent.core.state
        class: DialogueState

      # The config module for dialogue state featurizer
      SF:
        package: src.weather_state_featurizer
        class: WeatherStateFeaturizer
      # The config module for dialogue state tracker
      DST:
        package: src.weather_dst
        class: WeatherDialogueStateTracker
      # This is a warm-up policy. Use to initialize other Policy model based
      WP:
        package: src.weather_warm_up_policy
        class: WeatherWarnUpPolicy
      SynonymMapper:
        package: comet.agent.synonym_mapper.synonym_mapper
        class: SynonymMapper
      # The config module for policy. The policies will have some of sub policy
      # Each submodule will return a dialogue act, we will choose the best result based on
      # score or priority
      POLICY:
        policy_0:
            package: src.weather_warm_up_policy
            class: WeatherWarnUpPolicy
            arguments:
                algorithm:
                    priority: 3

      # The config module for nature language generator
      NLG:
        package: src.weather_template_nlg
        class: WeatherTemplateNLG
        arguments:
            template_path: data/agent_template_nlg.json

      # The config module for nature language understanding
      NLU:
        package: comet.agent.nlu.denver_onenet
        class: DenverOneNet

    # The config module for user simulatoren
    USER_SIMULATOR:
      package: src.weather_user_simulator
      class: WeatherAgendaBasedUS
      arguments:
        goals_path: null
        value_weights_path: null
        algorithm:
          patience: 5
          max_turns: 15
          required_slot: null
          slot_confuse_prob: 0.0
          value_confuse_prob: 0.0
          pop_distribution: [1.0]
        NLG:
          package: src.weather_template_nlg
          class: WeatherTemplateNLG
          arguments:
            template_path: data/usr_template_nlg.json
            role: usr