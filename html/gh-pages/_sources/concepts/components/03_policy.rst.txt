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