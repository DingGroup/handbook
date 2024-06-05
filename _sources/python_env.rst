Python environment
==================

Many of the calculations done in the lab use the Python programming language. The power of Python is in its convenience as a dynamic language, and in the vast number of libraries available for scientific computing.
To use these libraries, we use the Anaconda environment, which is a distribution of Python that comes with many of the libraries we need pre-installed. We can also easily install additional libraries using the `conda` package manager.
There are two ways to use Anaconda on the cluster.
One is to install anaconda under your directory and you will have full control over the environment. 
The other is to use the shared anaconda environment that is already installed for the lab.
To use the shared environment, you need to do the following.

Copy and paste the following lines to the file called ``.bashrc`` in your home directory on the cluster.

.. code-block:: bash

    # >>> conda initialize >>>
    # !! Contents within this block are managed by 'conda init' !!
    __conda_setup="$('/cluster/tufts/dinglab/shared_apps/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
    if [ $? -eq 0 ]; then
        eval "$__conda_setup"
    else
        if [ -f "/cluster/tufts/dinglab/shared_apps/miniconda3/etc/profile.d/conda.sh" ]; then
            . "/cluster/tufts/dinglab/shared_apps/miniconda3/etc/profile.d/conda.sh"
        else
            export PATH="/cluster/tufts/dinglab/shared_apps/miniconda3/bin:$PATH"
        fi
    fi
    unset __conda_setup
    # <<< conda initialize <<<

Then after you log out and log back in, you can use the shared environment by typing the following command.

.. code-block:: bash

    conda activate lab

The shared environment is called `lab`. It has many of the libraries we need pre-installed including ``numpy``, ``scipy``, ``openmm``, ``pytorch``, ``jax``, etc.
Visit `conda <https://conda.io/projects/conda/en/latest/user-guide/getting-started.html>`_ for more information on how to use conda.
Another tool for install python packages is `pip <https://pip.pypa.io/en/stable/>`_.