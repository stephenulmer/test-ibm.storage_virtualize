# Fixture For Testing ibm.storage_virtualize Against Different Execution Environments

## Installing ansible-naigator

    python3 -m venv venv
    source ./venv/bin/activate
    python3 -m pip install ansible-navigator

## Checking out git submodule

    git submodule init
    git submodule update    

The submodule remote will probably need to be updated to IBM's internal GitHub
instance. The reason to do it this way is that you can test against *any* commit
hash in the history, so you can do `git bisect` to find the commmit exposing the
issue.

## Prepare podman to fetch EEs

The AAP EEs are not availabel without Red Hat credentials, so we need to
login with podman:

    podman login registry.redhat.io

## Running ansible-navigator

The provided configuration file for Navigator tells it to fetch missing EEs, so the first run
will fetch the EE image, subsequent runs will not update it.  The configuration specifies an
EE, but we can change that on the comamnd-line with each run if we want.

The provided configuration also runs Navigator with regualr output to stdout, instead
of a TUI. For me this is just more straigh-forward.

To test against the EEs for AAP 2.4 and AAP 2.5, we can do the following:

    ansible-naigator run test.yml --eei registry.redhat.io/ansible-automation-platform-24/ee-supported-rhel9
    ansible-naigator run test.yml --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9

Note that we're just changing which container we use. We can test against any of the
provided EEs we want, or even custom ones (you could test against pre-releases for the
next version, for example).

## Accessing the EE

ansible-navigator also provides a way to access the container:

    ansible-navigator exec --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9 -- python3.11 -m pip list

You can also get an interactive shell inside the container:

    ansible-navigator exec --eei registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9 -- /bin/bash

You can even do the above to get the shell, and then run ansible-playbook by hand
inside the container, which may provide a debugging experience that is more familiar
(it does for me).

Letting ansible-navigator start the container for you (instead of using podman directly)
has the advantage that the working environment is already mapped, and you know
you're working exactly the way AAP will launch the automation.
