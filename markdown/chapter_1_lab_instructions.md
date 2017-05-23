## Lab Instructions

### Part 1: Familiarizing yourself with the environment

When you enter this page for the first time, your own environment will be fired
up on demand.  It consists of a single VM with a pre-deployed edX devstack and
enough resources to run it.  Once it's ready (this should only take a minute),
you'll see a terminal window with a command prompt below.

Once that happens, click on the window and feel free to browse.  Try running
the following:

    $ ls

You should see a directory called `openedxcon2017`: it is a git checkout of a
demo branch of this very course.  You'll use it later as a tool to understand
how the hastexo XBlock is used.

Now, start a `screen` session so you can use devstack effectively:

    $ screen

Next, become the `edxapp` user and start your LMS with the following commands.
It should take just a few seconds to start:

    $ sudo su edxapp
    $ paver devstack lms --fast
    ...
    Starting development server at http://0.0.0.0:8000/
    Quit the server with CONTROL-C.

To connect to it, you'll first need your devstack's public IP address.  To get
it, open a new screen window with `Ctrl-a c` (hold control and "a", release, then
the letter "c"), and enter:

    $ curl icanhazip.com

This should get you your `PUBLIC_IP`.  Now open a new browser tab, and enter
the following in you address bar:

    http://PUBLIC_IP:8000

You will see a vanilla Open edX instance, onto which the usual edX demo course
and users have been deployed.  Log in using:

    user: staff@example.com
    password: edx
    
### Part 2: Installing the hastexo XBlock and its prerequisites

The hastexo XBlock is responsible for firing up Heat stacks for your learners.
The easiest way to install it is via pip, into the edxapp python virtual env:

    $ sudo su edxapp
    $ pip install hastexo-xblock

Since this XBlock depends on Celery, edX's job queue, you also need to add it
to edX's LMS environment file, under `ADDL_INSTALLED_APPS`.  Edit that file:

    $ vim /edx/app/edxapp/lms.env.json

You should already see a "hastexo" entry at the very top, like so:

    "ADDL_INSTALLED_APPS": [
        "hastexo"
    ],

The XBlock also requires adding configuration to `XBLOCK_SETTINGS` on
the same file.  It looks like this:

    "XBLOCK_SETTINGS": {
        "hastexo": {
            "terminal_url": "/terminal",
            "ssh_dir": "/edx/var/edxapp/terminal_users/ANONYMOUS/.ssh",
            "ssh_upload": false,
            "ssh_bucket": "identities",
            "launch_timeout": 300,
            "suspend_timeout": 120,
            "task_timeouts": {
                "sleep": 5,
                "retries": 60
            },
    ...
    }

Finally, you also need to install and run Gateone, for terminal connectivity.
The easiest way is to use an Ansible role for it.  You can find one in
hastexo's fork of edx-configuration:

    $ git clone -b hastexo/master/hastexo_xblock https://github.com/hastexo/edx-configuration.git
    $ cd edx-configuration/playbooks
    $ ansible-playbook -c local -i "localhost," run_role.yml -e role=gateone

### Part III. How to use the XBlock in your courseware

As the `ubuntu` user, take a look in the following file:

    vim ~/openedxcon2017/sequential/chapter_1_lab_1.xml

Also make sure to add the "hastexo" module to the advance modules list in
`policies/policy.json`!
