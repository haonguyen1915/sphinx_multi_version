Step 3: Custom the State Featurizer (Optional)
==============================================

The dialogue state produced by DST module is often represented by a dict variable.
However, most existing policy models (e.g., Supervised policy, DQN and Policy Gradient) takes as
input a vector. Therefore, you have to convert the state to a vector representation
before passing it to the policy module.

The interfaces of state featurizer is defined in :class:`StateFeaturizer <comet.agent.core.dialogue_featurizers.state_featurizer.StateFeaturizer>`
including:

.. note::

    :class:`encode_state <comet.agent.core.dialogue_featurizers.state_featurizer.StateFeaturizer.encode_state>` takes an input as a list of :class:`Dialogue State <comet.agent.core.state.DialogueState>`, and returns the vector representation of state.

    :class:`encode_action <comet.agent.core.dialogue_featurizers.state_featurizer.StateFeaturizer.encode_action>` takes an input as a list of :class:`DialogueAct <comet.agent.core.action.DialogueAct>`, and returns the encode representation of action.

You can custom the way to encode state and action by inherits from :class:`StateFeaturizer <comet.agent.core.dialogue_featurizers.state_featurizer.StateFeaturizer>`.

For example in this sample domain we custom the additional signal::

    class WeatherStateFeaturizer(StateFeaturizer):
        def __init__(self, args) -> None:
            """
            Init dialogue  featurizer

            Args:
                args: dictionary containing the StateFeaturizer's arguments for Initialize
                    domain (Domain): The domain file
                    ontology: The ontology file path
            Returns: None
            """
            super(WeatherStateFeaturizer, self).__init__(args)

        @staticmethod
        def additional_state_feature():
            return ["is_terminal_state", "make_offer"]

