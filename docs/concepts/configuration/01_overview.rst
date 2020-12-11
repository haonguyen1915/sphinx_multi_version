.. _configuration:



Overview
--------

The configuration file defines the all the components of pipeline such as: polices, nature language
understanding (NLU), Natural language generator(NLG), Dialogue State Tracker(DST), State Featurezier (SF), User Simulator (US),...
that the system will use to run pipeline.
Here is an example of configuration run with comet.

.. code-block:: yaml

    GENERAL:
      interaction_mode: simulation
      models_path: ./models/BabeShop

      experience_logs:
        save: True
        load: False
        path: assets/BabeShop/v0.2.0/records/exp_dialogues.pkl
        test_path: null
        eval_path: assets/BabeShop/v0.2.0/records/test_fake_dialogues.pkl

      data:
        ontology: data/babe_ontology.json

        database:
          package: src.babe_database
          class: JSONDataBase
          arguments:
            path: data/babe_database.json

        domain: data/babe_domain.json

    DIALOGUE:
      num_dialogues: 200

      initiative: system
      show_act: True

    AGENT:
      role: system
      max_turns: 15
      train_interval: 200
      save_interval: 10

      DM:
        package: comet.agent.core.dialogue_manager.dialogue_manager
        class: DialogueManager

      SynonymMapper:
        package: comet.agent.synonym_mapper
        class: SynonymMapper
        arguments:
          path: models/babe_shop/nlg/auto_system_template_nlg.json

      DS:
        package: comet.agent.core.state
        class: DialogueState

      SF:
        package: comet.agent.core.dialogue_featurizers.state_featurizer
        class: StateFeaturizer
      DST:
        package: src.babe_dst
        class: BabeDialogueStateTracker
      WP:
        package: src.babe_warm_up_policy
        class: BabeWarmUpPolicy
      POLICY:
        policy_0:
          package: comet.agent.core.dialogue_policy.reinforcement_learning.dqn
          class: VanillaDQN
          arguments:
            WP: auto
            WUS: auto
            ontology: auto
            database: auto
            domain: auto
            train: True
            algorithm:
              training_epoch: 300
              mini_batch_size: null
              gamma: 0.9
            net:
              type: MLPNet
              hid_layers: [100]
              hid_layers_activation: relu
              clip_grad_val: null
              lr_scheduler_spec:
                name: StepLR
                step_size: 1000
                gamma: 0.999
              optim_spec:
                name: Adam
                lr: 0.001
              loss_spec:
                name: MSELoss
              gpu: False


            policy_path: models/camrest_policy/sys/sys_reinforce
      NLG:
        package: src.babe_template_nlg
        class: BabeTemplateNLG

      NLU:
        package: comet.agent.nlu.dummy_nlu
        class: DummyNLU
    USER_SIMULATOR:
      package: src.user_simulator.agenda_based_us
      class: FakeUS
      arguments:
        patience: 5
        NLG:
          package: src.babe_template_nlg
          class: BabeTemplateNLG
          arguments:
            template_path: applications/babe_sale_bot/data/nlg_usr_template.json
            role: usr

GENERAL
-------

- ``interaction_mode``:  Use to configure the way to interface with agent. Currently we have 2 option

    - ``text``: Interface with agent by usr message
    - ``simulation``: interface with agent by user simulator. To use this, US must be configured.
- ``models_path``:  The default base model directory. The agent will save and load all model weights in this directory if it's not provided in argument of each component.

- ``experience_logs``:  Handle internal experience logs. These are logs produced by the dialogue Recorder (under utilities) and contain information about what has happened during the dialogue(s). Agents use these logs as data for training.

    - ``save(bool)``: if True, the agent will save the dialogue records into ``path``
    - ``load(bool)``: if true, the agent will load the dialogue recoreds from ``path``
    - ``path(Text)``: Where to save or load dialogues records.
    - ``eval_path(Text)``: Where to load the data for validate training. Use only for policy training
    - ``test_path(Text)``: A path file to load test data for evaluation model. Use only for policy evaluation
- ``data``: These are data arguments and are added to the arguments of each module of each agent. In this template example we provide the ontology, database, and domain that every module needs.

    - ``ontology``: The path to the ontology. Accepted `.json` or `.yaml` file. See *data/babe_domain.json* for json database example
    - ``database``: The module configure for the database .db file. json, sql, neo4j. See data/babe_domain.json for json database example
    - ``domain``: The domain file path. Excepted `.yaml` and `.json` format. See data/babe_domain.json for example.

DIALOGUE
--------

- ``num_dialogues``: How many dialogue to run for
- ``initiative``: Which role has the initiative (speaks first): ``system`` or ``user``

AGENT
-----

-``role``: The role of the agent: ``system`` or ``user``.
-``max_turns``: When the conversation reach max_turns, agent will terminated the conversation
-``train_interval(int)``: This configure use only for online training mode. For example: ``train_interval``= 100 means after 100 dialogues, agent will get the recorded dialogues to train for each component.
-``save_interval(int)``: if ``save_interval``=100 mean after each 100 dialogues, the model of each component will be saved. Only use for training mode.

The component configuration (Ex: Dialogue manager, dialogue state tracker, nature language generator,
policy ...). Each component will configure following format

.. code-block:: yaml

    # Configure for dialogue managerment
    DM:
        package: comet.agent.core.dialogue_manager.dialogue_manager
        class: DialogueManager

    # Configure for synnonym mapper component. Optional
    SynonymMapper:
        package: comet.agent.synonym_mapper
        class: SynonymMapper
        arguments:
          path: models/babe_shop/nlg/auto_system_template_nlg.json

    # Configure for dialoguge state component
    DS:
        package: comet.agent.core.state
        class: DialogueState
    # Configure for dialogue state tracker
    DST:
        package: src.babe_dst
        class: BabeDialogueStateTracker
    ...
