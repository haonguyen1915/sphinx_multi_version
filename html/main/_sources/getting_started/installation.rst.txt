.. _installation:

Installation
============

Setting up a virtual environment
--------------------------------

`Conda <https://docs.conda.io/en/latest/>`_ can be used to set up a virtual environment with the version of Python required for Comet. If you already have a Python 3.6 or 3.7 environment you want to use, you can skip this section

1. `Download and install conda <https://docs.conda.io/projects/conda/en/latest/user-guide/install/>`_
2. Create a Conda environment with Python 3.6

.. code-block:: shell

    conda create -n comet python=3.6.5

3. Activate the Conda environment.

.. code-block:: shell

    source activate comet

Installing from library
-----------------------

.. code-block:: shell

    pip install http://minio.dev.ftech.ai/comet-packages-v0.2.0-dc98a9d3/comet-0.2.0-py3-none-any.whl

Installing from source
----------------------

Clone and Installing comet

.. code-block:: shell

    git clone https://gitlab.ftech.ai/nlp/research/comet.git
    cd comet/
    pip install .

To check that you have a fully functioning system, run the comet functional tests

.. code-block:: shell

    pytest ./tests
