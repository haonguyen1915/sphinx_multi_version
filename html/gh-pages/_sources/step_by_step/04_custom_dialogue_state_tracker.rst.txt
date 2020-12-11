Step 4: Custom the Dialogue State Tracker
=========================================

In the pipeline task-oriented dialog framework, the DST module encodes
the dialog history into a pre-defined state representation, which can be
either structured or unstructured. See :ref:`here <dialogue_state_tracker>` for the explain about DST.

In each turn, it takes as input the dialog act of user utterances, and updates
its internal state variable.

This instance contains the interface definition of dialog state
tracking module and some act-level DST module implementations.

Interface
---------

The interfaces of DST are defined in :class:`DialogueStateTracker <comet.agent.core.dialog_state_tracker.hard_dst.HardDialogueStateTracker>`
including:

- :class:`update_state <comet.agent.core.dialog_state_tracker.hard_dst.HardDialogueStateTracker.update_state>`: Takes as input the new utterances from user in each turn, and update the internal state variable of DST component
- :class:`update_state_sysact <comet.agent.core.dialog_state_tracker.hard_dst.HardDialogueStateTracker.update_state_sysact>`: Takes as input the action from system in each turn, and update the internal state.
- :class:`update_state_db <comet.agent.core.dialog_state_tracker.hard_dst.HardDialogueStateTracker.update_state_db>`. This function will update the current database results and may be change some signal in dialogue state such as: ``make_deny``, ``make_confirm``, ``make_offer``. So this method need to be implement because it depends on logic of each domain.
- :class:`initialize <comet.agent.core.dialog_state_tracker.hard_dst.HardDialogueStateTracker.initialize>`. reset the internal state variable for a new dialog session.

Sample implementation for ``WeatherAsking`` domain::

    class WeatherDialogueStateTracker(HardDialogueStateTracker):
        def __init__(self, args):
            super(WeatherDialogueStateTracker, self).__init__(args)

        def update_state_db(self, db_result: Dict, sys_requestable_semantic: Dict) -> DialogueState:
            """
                    This function will update the current database results. If the state belongs to a
                    'user' agent

                    Args:
                        db_result (Dict): The products result got from database
                        sys_requestable_semantic: (Dict): The slot that the system should request for semantic purpose

                    Returns:

                    """
            if db_result is None:
                raise ValueError(f"update_state_db: database results is provided")

            self.DState.sys_requestable_semantic = deepcopy(sys_requestable_semantic)
            _logger.db(f"sys_requestable_semantic: {sys_requestable_semantic}")
            if len(db_result) > 0:
                self.DState.make_offer = True

            self.DState.db_result = db_result
            return self.DState