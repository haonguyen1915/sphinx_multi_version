.. comet documentation master file, created by
   sphinx-quickstart on Tue Oct 13 21:15:06 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Comet's documentation!
======================

.. image:: _static/comet_1.png
   :alt: logo
   :align: center

The COMET is a pyhon framework for a dialogue system that follow the stanrdard dialogue system architectures. Aiming to quickly set up experiments with reusable components and compare large set of difference approaches, easily to create, train, evaluate and deploy tasked-oriented dialogue systems.
The conversational agent architectures can interacting with human users and with simulated users, respectively. Interacting with simulated users is a common practice used in the research community to jump-start learning

.. toctree::
   :maxdepth: 2
   :caption: Introduction
   :name: intro

   intro/introduction
   intro/motivation

.. toctree::
   :maxdepth: 2
   :caption: Getting Started
   :name: getting_started

   getting_started/installation
   getting_started/play_around

.. toctree::
   :maxdepth: 2
   :caption: Step By Step Guide
   :name: step_by_step

   step_by_step/00_overview
   step_by_step/01_define_the_hierarchy
   step_by_step/02_custom_the_dialogue_state
   step_by_step/03_custom_the_state_featurizer
   step_by_step/04_custom_dialogue_state_tracker
   step_by_step/05_define_a_hancraft_policy
   step_by_step/06_build_natatural_language_template
   step_by_step/07_build_user_simulator
   step_by_step/08_connect_all_components_by_config
   step_by_step/09_generate_training_data
   step_by_step/10_deploy_and_train_models
   step_by_step/Summarize

.. toctree::
   :maxdepth: 4
   :caption: Concepts

   concepts/domain
   concepts/configuration/index
   concepts/trainning_data/index
   concepts/components/index
   concepts/Reinforcement_learning
   concepts/chanel_connector
   concepts/Architecture
   concepts/test

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
