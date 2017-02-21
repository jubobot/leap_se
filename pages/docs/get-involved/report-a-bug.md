@title = "Report a bug (or a feature request)"
@summary = "How to submit a bug report or a feature request."

Something not working ? You found a bug, or want to propose a feature ?

Here's how you can get involved.

Please refer to the <a class='' href='/docs/platform/troubleshooting/known-issues'>Platform known issues</a> or the <a class='' href='/docs/client/known-issues'>Bitmask client known issues</a> before reporting a bug.
You might want to browse the <a class='' href='https://0xacab.org/leap/bitmask-dev/issues'>Bitmask client issues</a> or the <a class='' href='https://0xacab.org/leap/platform/issues'>Platform issues</a> as well.

## LEAP issue tracker

This is the LEAP <a class='' href='https://0xacab.org/leap'>LEAP issue stracker</a>.

* <a class='' href='https://0xacab.org/leap/bitmask-dev/issues/new'>Create a new Bitmask client issues</a>
* <a class='' href='https://0xacab.org/leap/bitmask-dev/issues/new'>Create a new Platform issues</a>

Please <a class='' href='https://0xacab.org/users/sign_in#register-pane'>register</a> before creating new issues.

## Anomymize your logs before pasting them in the LEAP issue tracker

Before you paste your logs you can use our log anonymizing script:

    git clone https://leap.se/git/scripts.git
    cd scripts

Edit the script and add your username and domain, then:

    ./clean-log.sh PATH_TO_LOGFILE > /tmp/LOGFILE.cleaned.log

Please double check before pasting nonetheless!
