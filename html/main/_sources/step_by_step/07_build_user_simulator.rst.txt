Step 7: Build the User Simulator
================================

User simulators are essential for training reinforcement learning (RL) based dialog models.
The performance of the simulator directly impacts the RL policy. However, building a good user
simulator that models real user behaviors is challenging

.. note::

    This is an in-depth tutorial to implement US based comet. Before you begin, read the :ref:`User Simulator <user_simulator>` for more detail.

Now, to finish implementing the User Simulator, we need to add the desired response for each dialogue system response. As we do so, we will define :class:`handle_single_system_act <comet.environment.user_simulator.agenda_base_us.agenda_based_us.AgendaBasedUS.handle_single_system_act>`::

    class WeatherAgendaBasedUS(AgendaBasedUS):
        def __init__(self, args):
            super(WeatherAgendaBasedUS, self).__init__(args)

        def handle_single_system_act(self, system_act: DialogueAct, in_goal: Goal, num_act: int):
            """
            Update agenda with system's response

            Args:
                system_act: The dialogue act from system
                in_goal: the simulated user's goal
                num_act: number of dialogue system acts

            Returns:

            """

            if system_act.intent == [DF_SYS_ACT.DENY, DF_SYS_ACT.THANK]:
                # If the system deny or not sure and no ask_confirm anything for user to choice
                # Just remove agenda, add thanks and bye act
                if num_act == 1:
                    self.agenda.clear()
                    self.agenda.push(DialogueAct(DF_USR_ACT.BYE, []))
                    self.agenda.push(DialogueAct(DF_USR_ACT.THANK, []))
            else:
                super(WeatherAgendaBasedUS, self).handle_single_system_act(system_act, in_goal, num_act)

.. note::

    See :class:`comet.environment.user_simulator.agenda_base_us.agenda_based_us.AgendaBasedUS` for full implementation. In this Class we already handle some default system response such as: ``INFORM, REQUEST``. The Goal will be generate based ``domain`` and ``ontology`` we provided.

    See :ref:`configure User Simulator <user_simulator_config>` to know the way to configure user simulator