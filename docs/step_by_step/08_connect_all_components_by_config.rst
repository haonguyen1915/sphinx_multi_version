.. _connect_all_components:

Step 8: Connect all components by Configuration
================================================

In this step, we will write a configuration to connect all components that we've have done. So we can run, train pipeline system

.. note::

    We may need to walk through :ref:`configuration section <configuration>`

1. Put domain data into ``GENERAL`` field. Supposed you prepared domain file in:

    - domain: ``data/weather_database.json``
    - ontology: ``data/weather_ontology.json``
    - database: ``data/weather_database.json``

.. code-block:: yaml

    GENERAL:
      ...
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

2. Configure policy component

.. code-block:: yaml

    AGENT:
      ...
      POLICY:
        policy_0:
          package: src.weather_warm_up_policy
          class: WeatherWarmUpPolicy
          arguments:
            priority: 3

3. Configure Dialogue state featurizer component

.. code-block:: yaml

    AGENT:
      ...
      SF:
        package: src.weather_state_featurizer
        class: WeatherStateFeaturizer

4. Configure Dialogue state tracker component

.. code-block:: yaml

    AGENT:
      ...
      DST:
        package: src.weather_dst
        class: WeatherDialogueStateTracker

5. Configure NLU component

.. code-block:: yaml

    AGENT:
      ...
      NLU:
        package: comet.agent.nlu.denver_onenet
        class: DenverOneNet

5. Configure NLG component

.. code-block:: yaml

    AGENT:
      ...
      NLU:
        package: src.weather_template_nlg
        class: WeatherTemplateNLG
        arguments:
            template_path: data/agent_template_nlg.json

6. Configure user simulator

.. code-block:: yaml

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

**Bellow is full configure for** ``WeatherAsking`` domain

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
        path: assets/BabeShop/v0.2.0/records/train_supervised_dialogues.json
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
