LOCK STORE
----------

Locking mechanism that issues tickets managing access to conversation IDs.
Tickets are issued in the order in which they are requested. A detailed
explanation of the ticket lock algorithm can be found at
http://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks.pdf#page=13

In comet's platform we support :class:`InMemoryLockStore <comet.agent.core.lock_store.InMemoryLockStore>`
and :class:`RedisLockStore <comet.agent.core.lock_store.RedisLockStore>`

InMemoryLockStore
"""""""""""""""""

**Configuration**

.. code-block:: yaml

    LOCKSTORE:
        package: comet.agent.core.lock_store
        class: InMemoryLockStore


RedisLockStore
""""""""""""""

**Configuration**

.. code-block:: yaml

    LOCKSTORE:
        package: comet.agent.core.lock_store
        class: RedisLockStore
        arguments:
            host: localhost
            port: 6379
            db: 1
            password: null
            use_ssl: False
            socket_timeout: 10

**Arguments**

- ``host``: The host of the redis server.
- ``port``: The port of the redis server.
- ``db``: The name of the database within Redis which should be used by comet.
- ``password``: The password which should be used for authentication with the Redis database.
- ``use_ssl``: `True` if SSL should be used for the connection to Redis. socket_timeout: Timeout in seconds after which an exception will be raised in case Redis doesn't respond within `socket_timeout` seconds.
