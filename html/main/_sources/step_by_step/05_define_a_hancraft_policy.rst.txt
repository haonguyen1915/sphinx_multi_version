Step 5: Build a Warm-up Policy
=================================

The term warm-up policy is used in reinforcement learning. We will say about in future tutorial.

To easy to understand. The policy the agent uses to pick an action given a state depends on whether
the the dialogue is in the warm-up stage or training stage. Warm-up is run before training to
fill the agent’s memory using usually a random policy. In this case the agent uses a very basic
rule-based policy during warm-up. In training, the behavior model is employed to pick an action.
In this case, use_rule means warm-up.

Each domain has a difference way to defined a warp-up policy. We need to implement by inherits from
:class:`DialoguePolicy <comet.agent.core.dialogue_policy.dialogue_policy.DialoguePolicy>`

.. note::
    You could visit :ref:`Policy section <policy>` to get more explanation about Policy.

    For implement rule_policy visit: :ref:`Rule policy section <rule_policy>`

Interface
---------

- :class:`next_action <comet.agent.core.dialogue_policy.dialogue_policy.DialoguePolicy.next_action>`: takes an input as a list of :class:`Dialogue State <comet.agent.core.state.DialogueState>` and outputs the next system action (List of :class:`DialogueAct <comet.agent.core.action.DialogueAct>`).
