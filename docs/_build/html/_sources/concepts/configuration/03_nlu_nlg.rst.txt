Natural language understanding(NLU)
----------------------------------

Natural language understanding make up your NLU pipeline and work sequentially to process user input
into structured output. There are components for entity extraction, for intent classification,
response selection, pre-processing, and more.

With NLU component we use denver core for this task. Fore more detail, feel free to visit: `Denver Core <https://nlp.pages.gitlab.ftech.ai/research/denver_core/index.html>`_

ULMFITClassifier and FlairSequenceTagger
""""""""""""""""""""""""""""""""""""""""

**Configuration**

.. code-block:: yaml

    AGENT:
        ...
        NLU:
        package: comet.agent.nlu.denver_flair
        class: DenverFlair
        arguments:
            SynonymMapper: auto
            ontology: auto

**Arguments**

- ``SynonymMapper``: SynonymMapper module (Optional)
- ``ontology``: The ontology instance
- ``intent_threshold``: confidence threshold
- ``model_path_ic``: The path of IC model. Use default path of not provide
- ``model_path_ner``: The path of NER model. Use default path of not provide

OneNet
""""""

**Configuration**

.. code-block:: yaml

    AGENT:
        ...
        NLU:
        package: comet.agent.nlu.denver_onenet
        class: DenverOneNet
        arguments:
            SynonymMapper: auto
            ontology: auto

**Arguments**

- ``SynonymMapper``: SynonymMapper module (Optional)
- ``ontology``: The ontology instance
- ``intent_threshold``: confidence threshold
- ``model_path``: The path of OneNet model. Use default path of not provide
- ``training``: If ``True`` it will train the NLU component with train data provided in ``train_args``
- ``testing``: If ``True`` it will test the NLU component with train data provided in ``train_args``
- ``train_args``: a dictionary containing data and all hyper-parameter to train with denver

.. _nlg:

Natural language generator(NLG)
-------------------------------

**Configuration**

.. code-block:: yaml

    AGENT:
        ...
        NLU:
            package: comet.agent.nlu.denver_flair
            class: DenverFlair
            arguments:
                SynonymMapper: auto
                ontology: auto

**Arguments**

- ``template_path``: the path of nlg template. If not provided, system will get the NLG default template path (Optional)
- ``product_key``: The name of product type. For example: ``object_type``, ``ticket``, ``weather``.... (Optional)
- ``role``: NLG for ``system`` or ``user``
- ``mode``: ``auto`` or ``manual``. Currently we only support ``auto`` mode. See :ref:`NLG section <build_nlg_template>` to know how to prepare NLG template with this mode.
