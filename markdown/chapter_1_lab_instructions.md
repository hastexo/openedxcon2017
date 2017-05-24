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

Note: You can return to the previous screen window by entering `Ctrl-a a`.
    
### Part 2: Installing the hastexo XBlock and its prerequisites

The hastexo XBlock is responsible for firing up Heat stacks for your learners.
The easiest way to install it is via pip, into the edxapp python virtual env.
On the second screen window, run the following:

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
hastexo's fork of edx-configuration.  As the regular `ubuntu` user:

    $ git clone -b hastexo/master/hastexo_xblock https://github.com/hastexo/edx-configuration.git
    $ cd edx-configuration/playbooks
    $ ansible-playbook -c local -i "localhost," run_role.yml -e role=gateone

### Part III. How to use the XBlock in your courseware

As the `ubuntu` user, take a look in the following file:

    vim ~/openedxcon2017/sequential/chapter_1_lab_1.xml

Also make sure to add the "hastexo" module to the advance modules list in
`policies/policy.json`!

At this point, you're ready to import the course into your devstack's LMS.  Run
the following as the `edxapp` user, from the `edx-platform` directory:

    $ sudo su edxapp
    $ ./manage.py lms --settings=devstack import /edx/var/edxapp/data \
       /home/ubuntu/openedxcon2017

Once the course is imported, go back to your LMS instance at
`http://PUBLIC_IP:8000`, log in as `staff@example.com`, enroll in the course
you just imported, entitled "Open edX Con 2017 (Demo)", and finally click on
the "Course" tab.

As you do this, keep the original tab open, and follow what happens in the
terminal.  You'll see the hastexo XBlock going through the process of firing up
a new Heat stack.

### Part IV. Working with olx-utils

As you work with OLX to create courseware, you'll notice that it has some
limitations.  Notably, you'll find yourself repeating the same XML stanzas over
and over again.  In the case of the hastexo XBlock this is particularly
painful, as you have to copy-paste every single parameter change to all
sections in the course where it is defined.

To mitigate this problem you can make use of `olx-utils`, a tool that
preprocesses the XML courseware using mako.  To install it, run the following
as the regular `ubuntu` user:

    $ sudo pip install olx-utils

You've examined the `openedxcon2017` repository at the `run/demo` branch, which
contains the output of olx-utils.  To see what the source material looks like,
run:

    $ cd ~/openedxcon2017
    $ git checkout demo

Look at the first lab section:

    $ vim sequential/chapter_1_lab_1.xml
    ...
    <%inherit file="lab.xml"/>\
    <%def name="section_name()">Chapter 1 Lab 1</%def>\
    <%block name="hastexo_tests">\
      <%text><test>
        #!/bin/bash
        # Check for logins
        logins=$(last ubuntu | grep ubuntu | wc -l)
        [ $logins -ge 1 ] || exit 1
        exit 0
      </test></%text>\
    </%block>

As you can see, the XML boilerplate is gone, and only what uniquely identifies
this section remains.  Where does the rest come from, though?  It's defined in
a parent file called `lab.xml`, as seen imported at the "inherit" line above.
You can see what it looks like at:

    $ vim include/sequential/lab.xml
