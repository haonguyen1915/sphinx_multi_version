
Overview
========

This Step-by-Step Guide outlines the methodology used to build today's most advanced and useful
conversational applications.

Taking a conversational application from conception to production typically entails completing
the ten implementation steps summarized below.

== ===
1  Define domain, ontology, database and role Hierarchy
2  Custom the Dialogue State (Optional)
3  Custom the State Featurizer (Optional)
4  Custom the Dialogue State Tracker (Optional)
5  Build a Warm-up Policy
6  Build the natural language generator template
7  Build the User Simulator
8  Connect all components by Configuration
9  Preparing representative training data
10 Train and Deploy models
== ===

Taking a simple use case as an example, this Guide walks through the methodology, highlighting the
key design decisions and technology components which underpin any great conversational experience.

In this guide, we will build a toy application: weather asking

.. note::
    For create a new module.

    - There are two ways to create a new module depending on its function. If a module, for example, implements a new way of performing NLU or dialogue policy, then you should write a class that inherits from the corresponding abstract class
    - If, however, a module does not fit one of the single agent basic components, for example, it performs Named Entity Recognition or predicts dialogue acts from text, then you must write a class that inherits from the link with class :class:`ConversationalModule <comet.abstract_module.conversational_module.ConversationalModule>` directly.