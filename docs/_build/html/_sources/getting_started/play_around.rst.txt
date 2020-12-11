Play Around
===========

In this place, we can quickly run some application such as: BabeShop or WeatherAsking.
These applications was built with comet. You can download the data or model by a simple `commandline interface`
to train or inference comet's system with a defined specified application with preparing nothing.
Ensure you've successfully :ref:`installed comet <installation>` before continue.

Running Babeshop's domain
-------------------------

1. Init ``BabeShop`` application

.. code-block:: shell

    comet init -d babe

.. note::

    We may need to change some variable in ``.env``. This variable control LOGGING, AXIOM dataset, models and setup axiom account

.. code-block:: shell

    AXIOM_EMAIL='user_email@ftech.ai'
    AXIOM_PASSWORD='password'

After init `BabeShop` project we will see something similar this

.. code-block:: json

    ├── config
    │   ├── configure_infer.yaml
    │   ├── configure_test.yaml
    │   └── configure_train.yaml
    ├── data
    │   ├── babe_database.json
    │   ├── babe_domain.json
    │   └── babe_ontology.json
    └── src
        ├── __init__.py
        ├── babe_database.py
        ├── babe_dst.py
        ├── babe_handcraft_policy.py
        ├── babe_state_featurizer.py
        ├── babe_template_nlg.py
        └── utils
            ├── __init__.py
            ├── age_parser.py
            ├── constants.py
            ├── money_parser.py
            ├── normalize_values.py
            └── object_type_mapping.py

2. Download pre-trained models

.. code-block:: shell

    comet model ls
    comet model download

.. note::

    All the information about version, ID model. You can set at ``.env``

After download we will see `models` folder similar this

.. code-block::

    models/
    └── BabeShop
        └── v0.2.0
            ├── nlu
            │   └── BabeDenverOneNet
            │       └── denver-onenet.tar.gz
            ├── policies
            │   ├── LSTMPolicy
            │   │   └── lstm_policy_model.ckpt
            │   ├── SVMPolicy
            │   │   └── svm_policy_model
            │   └── VanillaPolicy
            │       └── vanilla_policy_model.ckpt
            └── synonym_mapper
                └── SynonymMapper
                    └── synonym_mapper.json

3. Running inference

.. code-block:: shell

    comet run --config config/configure_infer.yaml

Sample dialogue with our `Use Case <https://gitlab.ftech.ai/-/snippets/39>`_

.. image:: ../_static/babe_sample_dialogue.png
    :alt: weather_sample
    :align: center

Training with Babeshop's domain
-------------------------------

1. Download dataset

.. code-block:: shell

    comet data ls
    comet data download

2. Train supervised policy and NLU

To train supervised policy and denver NLU, we run with train configure.

.. code-block:: shell

    comet run --config config/configure_train.yaml