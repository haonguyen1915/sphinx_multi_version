.. _build_nlg_template:

Step 6: Build a Natural Language Template
=========================================

In a pipeline task-oriented dialog framework, the NLG module takes as input
the dialog act of system action, and convert it to a natural language
utterance, which is the system response that user receives.

There are several way to build a natural language generator such as: model based, rule based, template based ...
In comet we've implement a template based natural language generator.

.. note::

    You can build your own NLG. And follow interface of :class:`generate_output <comet.agent.core.nlg.template_based_nlg.TemplateNLG.generate_output>` method that take an input as List of :class:`DialogueAct <comet.agent.core.action.DialogueAct>`, current :class:`Dialogue State <comet.agent.core.state.DialogueState>` and return a natural language (Text)

    See :class:`TemplateNLG <comet.agent.core.nlg.template_based_nlg.TemplateNLG>` for more interface sample.

In this guide we use :class:`TemplateNLG <comet.agent.core.nlg.template_based_nlg.TemplateNLG>` which is implemented. So all we need to do is prepare NLG template following format::

    {
        "intent": {
            "slot": [utt1, utt2, ...],
            "slot1;slot2;...": [utt1, utt2, ...]
        },
        "intent1;intent2;": {
            "slot": [utt1, utt2, ...],
            "slot1;slot2;...": [utt1, utt2, ...]
        }
    }

.. note::

    ``slot1;slot2`` or ``intent1;intent2`` must be sorted by alphabet.


Below is a sample NLG template for ``WeatherAsking`` domain in this guide:

**For Agent**:

.. code-block:: json

    {
        "offer": {
            "none;": []
        },
        "inform": {
            "date;": ["The  date is {date}"],
            "temp;": ["The  temperature is {temp}"]
           },
        "request": {
            "data;": ["What's date you want to search?"]
        },
        "greet": {
            "none;": ["Hello, I'm weather asking assistant?"]
        },
        "thanks": {
            "none;": ["Thank you so much"]
        },
        "deny": {
            "none;": ["Sorry, we have no weather information for that day"]
        },
        "bye": {
            "none;": ["Bye, see u late"]
        },
        "i_am_bot": {
            "none;": ["Yeah, i'm bot"]
        },
        "cant_help": {
            "none;": ["Sorry, we didn't get that"]
        }
    }

**For user**:

.. code-block:: json

    {
        "offer": {
            "none;": []
        },
        "inform": {
            "date;": ["date is {date}"],
            "temp;": ["The  temperature is {temp}"]
           },
        "request": {
            "date;": ["What's date?"],
            "temp;": ["What's temperature?"],
            "weather;": ["What's the weather?"]
        },
        "greet": {
            "none;": ["Hello bot?"]
        },
        "thanks": {
            "none;": ["Thank bot"]
        },
        "bye": {
            "none;": ["Bye, Bot"]
        },
        "ask_is_bot": {
            "none;": ["are you bot"]
        }
    }

Once NLG template've done, we just give the path for :class:`TemplateNLG <comet.agent.core.nlg.template_based_nlg.TemplateNLG>`. Sometime we may want to add some desired message such as when make offer a product. We can inherits from ``TemplateNLG`` class::

    class WeatherTemplateNLG(TemplateNLG):
        def __init__(self, args):
            """
            Initialize for template nature language generator

            Args:
                args:
                    path: (Text) a template json file, if it's not provided, system wil get in
                        default file. (Optional)
                    role: (Text) usr or system. (Optional)
                    mode: (Text) manual or auto. (Optional)
            """
            super(WeatherTemplateNLG, self).__init__(args)

        def generate_output(self, dialogue_acts: List[DialogueAct], state: Any = None) -> Text:
            """
            NLG for Babe domain

            Args:
                dialogue_acts: List of dialogue act
                state: database results

            Returns: sentence

            """
            if not self.template:
                _logger.trace(f"NLG did not load template correctly with {self.template_path}. "
                              f"Please check again. Exit", level=logger.LEVEL.ERROR)
                return str([str(act) for act in dialogue_acts])

            if self.role == constants.SYSTEM and SYS_ACT.OFFER in [act.intent for act in dialogue_acts]:
                nl_message = f"This is the weather info for your constraint: " \
                             f"{reformat_dict(state.db_result)}"
            else:
                nl_message = super(WeatherTemplateNLG, self).generate_output(dialogue_acts, state)

            return nl_message

.. note::

    See :ref:`configure nlg <nlg>` to know the way to configure template with NLG comet