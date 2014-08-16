## VirtualEnv Config

Manages Virtual Environments even better. All credit due to the infamous [Dan Sackett](https://github.com/dansackett) whom I forked this from and revised for myself.


### VirtualEnv and VirtualEnv Wrapper
Install Python PIP (Package Manager) if not already installed.

    sudo apt-get install python-setuptools
    sudo easy_install pip

Globally Install our two items

    sudo pip install virtualenv virtualenvwrapper

Keep the Virtual Environments in a central location. The main users `~/` root folder run these commands:

    mkdir ~/.virtualenvs
    echo "export WORKON_HOME=~/.virtualenvs" >> ~/.bashrc
    echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
    echo "export PIP_VIRTUALENV_BASE=~/.virtualenvs" >> ~/.bashrc

These steps will setup the virtualenv and virtualenvwrapper to work together.
We can do one last thing and add this line::

    echo "alias mkvirtualenv='mkvirtualenv --no-site-packages --distribute'" >> ~/.bashrc

This alias will allow you to easily create a new virtualenv with the command
mkvirtualenv without using site packages and using distribute instead of
setuptools.::

    source ~/.bashrc

That will reload the bash prompt and get everything ready to use. The next
thing I like to do is setup the virtualenv to work well with my current
projects folder. I use a projects folder in the home directory for my web work
and applications. Edit your postactivate file (**~/.virtualenvs/postactivate**)::

    # source postactivate hook
    _HOOK_PATH=bin/postactivate
    _PROJECT_FILE=$VIRTUAL_ENV/$VIRTUALENVWRAPPER_PROJECT_FILENAME

    if [ -s $_PROJECT_FILE ]; then
        export _PROJECT_DIR=$(cat $_PROJECT_FILE)
        _HOOK=$_PROJECT_DIR/$_HOOK_PATH
        [ -f $_HOOK ] && . $_HOOK
    fi

Your postdeactivate file (**~/.virtualenvs/postdeactivate**)::

    # source postdeactivate hook
    _HOOK_PATH=bin/postdeactivate

    if [ -n "$_PROJECT_DIR" ]; then
        _HOOK=$_PROJECT_DIR/$_HOOK_PATH
        [ -f $_HOOK ] && . $_HOOK
        unset _PROJECT_DIR
    fi

Your postmkvirtualenv file (**~/.virtualenvs/postmkvirtualenv**)::

    #!/bin/bash
    # This hook is run after a new virtualenv is activated.

    proj_name=$(echo $VIRTUAL_ENV|awk -F'/' '{print $NF}')
    [ ! -d ~/projects/$proj_name ] && mkdir -p ~/projects/$proj_name
    add2virtualenv ~/projects/$proj_name
    cd ~/projects/$proj_name

Your predeactivate file (**~/.virtualenvs/predeactivate**)::

    #!/bin/bash
    # This hook is run before every virtualenv is deactivated.

    cd

After these are edited, you will be able to create a new virtualenv with
**mkvirtualenv project_name** and when you do a folder named **project_name**
will be created in ~/projects. You will automatically CD into that directory
and when you deactivate you will be placed back in your home folder. Pretty
neat.
