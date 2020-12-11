.. _domain:

Domain
------
To model and understand natural language, every conversational application relies on a hierarchy of
machine learning classifiers. This family of machine learning models, broadly defined as the Natural
Language Processing (NLP) component of an application. So the domain defines the universe in which
your assistant operates. It specifies the intents, `user_act_set`, `system_act_set` that
your bot should know about

Here is a full example of a domain, taken from the weather asking example:

.. image:: ../_static/domain.png


Ontology
--------
Ontology matching is an essential task for the management of the semantic heterogeneity in open
environments. The matching process aims at generating a set of correspondences (i.e., an alignment)
between the entities of different ontologies. Two ‘paradigms’ organise the field.

Ontology-based conversating is indeed a way of domain-driven conversation. We used ontologies in our
work for two purposes:

- to store the knowledge
- to navigate through the domain

The knowledge base component takes part in many dialogue systems. After the NLU component turns queries
into a logical form, next step is to interrogate the knowledge base for the answer generation. In BabeShop
domain ontologies, one potentially needs to store.

- A range of babe's products: ghe a, , xe day, ...
- attributes of these products: color, price, promotion, guarantee, weight...
- A list of attributes that user can be inform or request
- A list of attributes that sytem can ve inform or reqquest

For instance, it is not possible to generate an answer without knowing the product. The knowledge
base module provides information to the answer generation module.

In our VA the ontology also drives the conversation: keeps the context, provides a basis to produces
what to say next. The knowledge base component and the dialogue management components.

Here is a full example of a ontology, taken from the weather asking example:

.. image:: ../_static/ontology.png

Database
--------

Database containing the information on your products. Using comet, a modern bot-building platform
that is designed to easily build highly conversational, that helps clients
find a product they want
