Step 1: Define domain, ontology, database
=========================================

For implementing a task-oriented dialog system in Comet the user needs to specify three components
that constitute the domain of the dialog system:
- Domain
- Ontology
- Database

These components defined a domain which are explained further in :ref:`Domain Section <domain>`.

Now we will prepare each part for ``Weather asking`` application.

Domain
------

Comet supported domain in two format: ``.json`` and ``.yaml``.

.. code-block:: json

    {
        "name": "WeatherAsking",
        "usr_act_set": [
            "greet",
            "thanks",
            "bye",
            "ask_is_bot",
            "inform",
            "request",
            "fallback"
        ],
        "system_act_set": [
            "offer",
            "inform",
            "request",
            "greet",
            "thanks",
            "bye",
            "i_am_bot",
            "cant_help"
        ]
    }

.. note::

    - ``name``: The name of domain.
    - ``usr_act_set``: Define all possible action which user can take
    - ``system_act_set``: Define all possible action that system can take

Ontology
--------

Comet supported ontology in two format: ``.json`` and ``.yaml``.

.. code-block:: json

    {
      "type": "WeatherAsking",
      "usr_informable": [
        "date",
        "temp",
        "weather"
      ],
      "system_informable": [
        "date",
        "temp",
        "weather"
      ],
      "usr_requestable": [
        "date",
        "temp",
        "weather"
      ],
      "system_requestable": [
        "date"
      ],
      "informable": {
        "date": ["1/1", "1/2", "1/3", "1/4"],
        "temp": ["41 degree", "42 degree", "43 degree", "44 degree"],
        "humidity": [10, 11, 12],
        "weather": ["hot", "light rain", "cloudy"]
      }
    }

.. note::

    - ``type``: The name of ontology.
    - ``usr_informable``: Define all possible attributes that user can inform
    - ``usr_requestable``: Define all possible attributes that user may be request
    - ``system_informable``: Define all possible attributes that system can inform
    - ``system_requestable``: Define all possible attributes that system may be request
    - ``informable``: It's a dictionary which is contain all possible values of each attribute

Database
--------

We can use any database such as neoj4, sql, redis, ... In this guide, to make everything to be easy. We
use JsonDatabase which is query data from dictionary.

Database data sample for ``WeatherAsking`` domain

.. code-block:: json

    {
        "0": {
            "date": "1/1",
            "temp": "40 degree",
            "humidity": 10,
            "weather": "hot"
        },
        "1": {
            "date": "1/2",
            "temp": "41 degree",
            "humidity": 12,
            "weather": "light rain"
        }
    }

After preparing database data. Now we need to implement ``class`` JsonDatabase to query information

We implement ``JsonDatabase Class`` which inherits from :class:`Database <comet.domain.database.DataBase>`

.. note::

    :class:`Database <comet.domain.database.DataBase>` is abstract class, we need to implement :class:`db_lookup <comet.domain.database.DataBase.db_lookup>` method with:

    - input: a dictionary of attribute and it's value. For example::

        {
            date: "1/1",
            temp: "40 degree"
        }

    - output: List of products. For example::

        {
            "0": {
                "date": "1/1",
                "temp": "40 degree",
                "humidity": 10,
                "weather": "hot"
            },
            "1": {
                "date": "1/2",
                "temp": "41 degree",
                "humidity": 12,
                "weather": "light rain"
            }
        }



Here is the example of implementation for ``JSONDataBase``::

    from collections import defaultdict
    import copy
    from typing import Text, Dict, List
    from comet.domain.database import DataBase
    from comet.agent.core.state import DialogueState
    from comet.lib import load_json, load_yaml, logger, set_attr
    _logger = logger.get_logger(__name__)

    class JSONDataBase(DataBase):
        def __init__(self, args: Dict):
            """
            Constructor for a KBHelper

            Args:
                args: a dictionary containing
                    path: The path of json database
            """
            super(JSONDataBase, self).__init__(args)
            self._check_args_sanity(args)

            self.db_path: Text = args.get("db_path", '')

            if self.db_path.endswith(".yaml"):
                self.babe_kb: Dict = load_yaml(self.db_path)
            elif self.db_path.endswith(".json"):
                self.babe_kb: Dict = load_json(self.db_path)
            else:
                raise TypeError(f"Database file only support json or yaml style not support "
                                f"{self.db_path}")
            self.cached_kb: Dict = defaultdict(list)
            self.cached_kb_slot: Dict = defaultdict(list)

        def db_lookup(self, belief_state: Dict, max_db: int = 10, ignore_attrs: List = None,
                      **kwargs) -> Dict:
            """
            Return the available product in the babe_kb based on the current constraints

            Args:
                belief_state (DialogueState): belief_state use to lookup db
                max_db (int): Get the products from DB with maximum is max_db
                ignore_attrs: list attribute will be ignored when searched

            Returns (Dict): a dictionary of all products matched

            """
            ret_result = []
            slot_filled = copy.deepcopy(belief_state)
            constrain_keys = slot_filled.keys()
            constrain_keys = [k for k in constrain_keys if slot_filled[k] != SLOT_VALUE.I_DO_NOT_CARE]
            for id in self.babe_kb.keys():
                kb_keys = self.babe_kb[id].keys()
                if len(set(constrain_keys).union(set(kb_keys)) ^ (
                        set(constrain_keys) ^ set(kb_keys))) == len(constrain_keys):
                    match = True
                    for idx, k in enumerate(constrain_keys):
                        if str(slot_filled[k]).lower() == str(self.babe_kb[id][k]).lower():
                            continue
                        else:
                            match = False
                    if match:
                        ret_result.append((id, self.babe_kb[id]))
            ret_result = ret_result[0:max_db]
            ret_result = dict(ret_result)
            return ret_result



