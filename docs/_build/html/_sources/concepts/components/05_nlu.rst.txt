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
