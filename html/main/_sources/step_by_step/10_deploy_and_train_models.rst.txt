Step 10: Train and deploy models
================================

Once your application has been built, Now it easy to train, test locally by configuration.
Now, all we need to do is write a configure to do what we want to do.

Train supervised policy and NLU
-------------------------------

1. Set interact mode to text

.. code-block:: yaml

    GENERAL:
      # Can be: simulation, text
      interaction_mode: text

2. Set training data path that you have done

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
            test_path: assets/weather_asking/v0.2.0/records/test_supervised_dialogues.pkl
            eval_path: assets/weather_asking/v0.2.0/records/eval_supervised_dialogues.pkl

3. Enable ``training`` model in policy component

.. code-block:: yaml

    AGENT:
      ...
      POLICY:
        policy_0:
          package: src.weather_warm_up_policy
          class: WeatherWarmUpPolicy
          arguments:
            training: True
            testing: True
            algorithm:
                priority: 3


4. Enable ``training`` mode and training data path for NLU component

.. code-block:: yaml

    AGENT:
      ...
      NLU:
        package: comet.agent.nlu.denver_onenet
        class: DenverOneNet
        arguments:
            training: 1
            train_args:
                train_path: assets/WeatherAsking/v0.2.0/nlu/train.csv
                test_path: assets/WeatherAsking/v0.2.0/nlu/test.csv

Bellow is full configure to train policy and nlu

.. code-block:: yaml

    GENERAL:
      # Can be: simulation, text
      interaction_mode: text
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
        save: false
        load: True
        path: assets/weather_asking/v0.2.0/records/train_supervised_dialogues.pkl
        test_path: assets/weather_asking/v0.2.0/records/test_supervised_dialogues.pkl
        eval_path: assets/weather_asking/v0.2.0/records/eval_supervised_dialogues.pkl

      # These are data arguments and are added
      # to the arguments of each module of each agent. In this template example we
      # provide the ontology, database, and domain that every module needs.
      data:
        # The path to the ontology .json file
        ontology: applications/weather_asking/data/weather_ontology.json

        # The path to the database .db file. json, sql, neo4j
        database:
          package: src.weather_database
          class: JSONDataBase
          arguments:
            db_path: applications/weather_asking/data/weather_database.json

        #The current domain
        domain: applications/weather_asking/data/weather_domain.json

    DIALOGUE:
      # How many dialogues to run for
      num_dialogues: 1

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
      # The config module for policy. The policies will have some of sub policy
      # Each submodule will return a dialogue act, we will choose the best result based on
      # score or priority
      POLICY:
        policy_0:
          package: comet.agent.core.dialogue_policy.supervised_learning.vanilla_classifier_policy
          class: VanillaPolicy
          arguments:
            training: True
            algorithm:
                priority: 1
                training_epoch: 500
                batch_size: 32

      # The config module for nature language generator
      NLG:
        package: src.weather_template_nlg
        class: WeatherTemplateNLG
        arguments:
            template_path: data/agent_template_nlg.json

      # The config module for nature language understanding
      NLU:
        package: src.weather_nlu
        class: WeatherDenverOneNet
        arguments:
          training: 1
          train_args:
            train_path: assets/WeatherAsking/v0.2.0/nlu/train.csv
            test_path: assets/WeatherAsking/v0.2.0/nlu/test.csv

Reinforcement learning for policy
---------------------------------

N/A

Deploy
------

N/A