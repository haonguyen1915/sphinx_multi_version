Components
==========

Dialogue manager
----------------

What is the Dialogue Manager?

A complex system can usually be decomposed into different layers of abstraction. Every layer uses
the results of the previous layer and its results are used for the next layer. After understanding
what the user meant, the bot needs to select the correct reply. This reply is based on what was
said and which knowledge the bot has. Depending on the complexity of the conversation this is no
easy task. Managing this is done by the so-called Dialogue Management (DM)

So basically the Dialogue Manager are responsible for two tasks:

.. _dialogue_state_tracker:

Dialogue state tracker
""""""""""""""""""""""

The first one is state tracking and it actually acquires some hand-crafted states. And, what it
does is, it can query the external database or knowledge base for some additional information. It
actually tracks the evolving state of the dialogue and it constructs the state estimation after every
utterance from the user.

.. _policy:

Policy
""""""

Another part is policy learner, that is the part that takes the state estimation as input and
chooses the next best action from the dialogue system from the agent.

It is actually a mapping from dialogue state to agent act. Imagine that we have a conversation with
the user. We collect some information from him or her, and we have that internal state that tells
us what the user essentially wants, and we need to take some action to continue the dialogue. And we
need that mapping from dialogue state to agent act, and this is what dialogue policy essentially is

You've heard about the state. So what is the Dialogue State

.. _dialogue_state:

Dialogue State
""""""""""""""

Because with every new utterance, user can specify more details or change its intent, and that
all affects our state. And you can think of state as something describing what the user ultimately
wants. And then, when we have the state, we have to do something, we have to react, and we need to
learn policy, and that is a part of a dialogue manager as well

Natural language understanding(NLU)
-----------------------------------

Natural language understanding (NLU) is a sub-topic of natural language processing, which involves breaking down the human language into a machine-readable format.
The goal of NLU is to extract structured information from user messages.
This usually includes the user's intent and any entities their message contains. You can add extra
information such as regular expressions and lookup tables to your training data to help the model identify
intents and entities correctly.

There are two sub-task for NLU: intent classifier and slot tagger

Intent classifier
"""""""""""""""""

We can use any model on bag-of-words with n-grams and TF-IDF, just use classical approaches of
text mining, or you can use some recurrent architecture and you can use LSTM cells, GRU cells,
or any other. You can also use convolutional networks and you can use 1D convolutions.
And the study actually shows that CNNs can perform better on datasets where the task is essentially
a key phrase recognition task and it can happen in some sentiment detection datasets,

Slot tagger
"""""""""""

This is a bit more difficult task. It can use handcrafted rules like regular expressions,
But that approach doesn't scale because the natural language has a huge variation in how we can
express the same thing. So, it makes sense to do something data driven here. We can use conditional
random fields, that is a rather classical approach, or you can use RNN sequence-to-sequence model,
when you have encoder and decoder, and a funny fact is that you can still use convolutional networks
for a sequence-to-sequence task as well, and we can add attention to any of these models, any
sequence-to-sequence model.

In the scope of comet platform, we use `Denver lib <https://nlp.pages.gitlab.ftech.ai/research/denver_core/index.html>`_ for NLU tasks

Natural language generator(NLG)
-------------------------------

Natural Language Generation, as defined by Artificial Intelligence: Natural Language Processing Fundamentals,
is the “process of producing meaningful phrases and sentences in the form of natural language.”

In other words, structured data is presented in an unstructured manner to the user. Think of NLG is
the inverse of NLU.

With NLU we are taking the unstructured conversational input from the user (natural language) and
structuring it for our software process. With NLG, we are taking structured data from backend and
state machines, and turning this into unstructured data. Conversational output in human language.

- NLU takes up the understanding of the data based on grammar, the context in which it was said and decide on intent and entities.
- DM converts a text into structured data.
- NLG generates a text based on structured data.


.. _user_simulator:

User simulator
--------------

.. image:: ../_static/dialogue_flow_us.png

User simulators are used to simulate an actual user so that an agent can be trained much faster than
if real users were forced to sit down and give it multiple hours of training. User simulator research
for chatbot domains is a hot research topic.

The one covered in our platform is the very well known Agenda-Based User Simulator, This means
that a user has a goal for that episode and it takes actions in accordance to this goal while keeping
track of an internal state that allows it follow the dialogue to take informed actions.

What is the Goal?
"""""""""""""""""

User goals are sampled from a corpus of real dialogue or hand-crafted (or both). Each goal is made up
of inform slots and request slots just like an action but without the intent. There are a couple standard
ways to grab the goals from a corpus

- All of the slots (request and inform) from the initial user action in an episode make a single goal
- All slots from all user actions in an episode are combined to make a single goal

A few examples:

.. code-block::

    {'request_slots': {'date': 'UNK', 'theater': 'UNK'}, 'inform_slots': {'numberofpeople': '4', 'moviename': 'zootopia', 'starttime': 'matinee'}}
    {'request_slots': {'theater': 'UNK'}, 'inform_slots': {'city': 'la', 'numberofpeople': '2', 'distanceconstraints': 'downtown', 'video_format': '3d', 'starttime': '7pm', 'date': 'tomorrow', 'moviename': 'creed'}}

Agenda-Based User Simulator
"""""""""""""""""""""""""""

.. figure::  ../_static/us.png
   :align:   center

The Agenda-Based User Simulator was proposed by Schatzmann and is explained in detail in this `paper <https://www.semanticscholar.org/paper/Statistical-User-Simulation-with-a-Hidden-Agenda-Schatzmann-Thomson/fdde1f5ee1734e562298d6d4ca6893921276f0d2>`_.
Conceptually, the simulator maintains an "agenda" of things to say, which is usually implemented as
a stack. When the simulator receives input, it consults its policy (or its set of rules) to see what
content to push into the agenda, as a response to the input. After some housekeeping (e.g. removing
duplicates or content that is no longer valid), the simulator will pop one or more items off the
agenda that will be used to formulate its response.

The Agenda-Based User Simulator also has an error simulation module, that can simulate speech
recognition / language understanding errors. Based on some probabilities, it will distort the output
dialogue acts of the simulator - the intent, slot, or value (different probability for each).

Now we will dive into each element of a User simulator

User Goal
"""""""""

Sampled from a real corpus / hand-crafted list

- Information access
    -  Inflexible: the user has specific entries for search

    .. code-block::

        request_slot: rating, inform_slots: {moviename=“A”, …}

- Task completion
    - Inflexible: the user has specific rows for search

    .. code-block::

        request_ticket, inform_slots: {moviename=“A”, …}

    - Flexible: some slot values are unknown or can be suggested

    .. code-block::

        request_ticket, request_slots: {time, theater},
        inform_slots: {moviename=“A”, …}

User Agenda Mode
""""""""""""""""

User agenda defines how a user interacts with the system in the dialogue level

- Random order: random sample a slot for informing or request
- Fixed order: define a list of ordering slots for informing or request

Frame-Level Interaction
"""""""""""""""""""""""

DM receives frame-level information

- No error model: perfect recognizer and LU
- Error model: simulate the possible errors

.. figure::  ../_static/frame_mode.png
   :align:   center

Natural Language Level Interaction
""""""""""""""""""""""""""""""""""

User simulator sends natural language

- No recognition error
- Errors from NLG or LU

.. figure::  ../_static/nlg_level.png
   :align:   center

Error Model
"""""""""""

There are several for error model

- Handcraft: Randomly generate errors by
    - Replacing with an incorrect intent
    - Deleting an informable slot
    - Replacing with an incorrect slot (value is original)
    - Replacing with an incorrect slot value

- Model-based
    - Simulate the errors based on the ASR or LU confusion

Reward Function
"""""""""""""""

It's Success measurement. Check whether the returned results satisfy the
predefined user goal.

- Success: large positive reward
- Fail: large negative reward

